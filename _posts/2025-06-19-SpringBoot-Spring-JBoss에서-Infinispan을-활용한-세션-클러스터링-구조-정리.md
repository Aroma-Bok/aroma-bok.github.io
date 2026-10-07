---
title: "[SpringBoot] Spring + JBoss에서 Infinispan을 활용한 세션 클러스터링 구조 정리"
date: 2025-06-19 17:16:05 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["infinispan을 활용한 세션 클러스터링", "spring + jboss에서 infinispan", "spring jboss 세션 클러스터링 구조", "스프링 제이보스 클러스터링"]
tistory_url: "https://aroma-bok.tistory.com/entry/SpringBoot-Spring-JBoss%EC%97%90%EC%84%9C-Infinispan%EC%9D%84-%ED%99%9C%EC%9A%A9%ED%95%9C-%EC%84%B8%EC%85%98-%ED%81%B4%EB%9F%AC%EC%8A%A4%ED%84%B0%EB%A7%81-%EA%B5%AC%EC%A1%B0-%EC%A0%95%EB%A6%AC"
---
**프로젝트를 진행하면서, 로그인 파트를 담당하게 되었고, 세션 클러스터링까지 고려해야 하는 상황이 생겨서 이를 해결한**

**사례를 정리해 봤다.**

* * *

### **1\. 세션 클러스터링이란?**

-   **WAS 인스턴스가 2개 이상일 때,**  
    **로그인한 사용자 세션을 모든 서버에서 동일하게 접근할 수 있도록 하는 구조**
-   **Spring에서는 기본적으로 서버 메모리 내 HttpSession만 사용하므로**  
    **여러 서버에서 로그인 세션을 공유하려면 세션 클러스터링 필요**

### **2\. 왜 Infinispan을 사용했나?**

<table style="border-collapse: collapse; width: 100%; height: 88px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 22px;"><td style="width: 26.5117%; height: 22px;"><b><span style="font-family: 'Noto Serif KR';">항목</span></b></td><td style="width: 73.4883%; height: 22px;"><b><span style="font-family: 'Noto Serif KR';">이유</span></b></td></tr><tr style="height: 22px;"><td style="width: 26.5117%; height: 22px;"><b><span style="font-family: 'Noto Serif KR';">✅ JBoss 기본 내장</span></b></td><td style="width: 73.4883%; height: 22px;"><b><span style="font-family: 'Noto Serif KR';">Infinispan은 JBoss EAP/Wildfly에 기본 내장된 고성능 분산 캐시</span></b></td></tr><tr style="height: 22px;"><td style="width: 26.5117%; height: 22px;"><b><span style="font-family: 'Noto Serif KR';">✅ 고성능 분산 캐시</span></b></td><td style="width: 73.4883%; height: 22px;"><b><span style="font-family: 'Noto Serif KR';">세션뿐 아니라 사용자 인증, 권한 등의 캐싱에도 활용 가능</span></b></td></tr><tr style="height: 22px;"><td style="width: 26.5117%; height: 22px;"><b><span style="font-family: 'Noto Serif KR';">✅ 클러스터 이벤트 리스닝</span></b></td><td style="width: 73.4883%; height: 22px;"><b><span style="font-family: 'Noto Serif KR';">캐시 변경 이벤트를 각 노드에 전파 가능 (@CacheEntryCreated, @CacheEntryRemoved)</span></b></td></tr></tbody></table>

### **3\. 전체 아키텍처 개요**

![](/assets/img/posts/318/1.png)

-   **사용자 로그인 → HttpSession 생성**
-   **sessionId → Infinispan dist 캐시에 저장**
-   **각 노드에서 캐시 이벤트를 수신하여 세션 관리 동기화**

### **4\. 로그인 시 세션 처리 흐름**

**1\. 로그인 성공**

**2\. HttpSession 생성  
**

```java
HttpSession session = request.getSession(true);
```

**3\. 세션에 사용자 정보 저장 (setAttribute)**

```java
session.setAttribute("USER_DETAIL", user); // 세션에 사용자 객체 저장
String sessionId=session.getId();
```

**4.InfinispanCacheService에서 sessionId 저장 (putForExternalRead)**

```java
infinispanCacheService.put("dist", Lgn.getIntgAcntId(), sessionId);
```

**5\. Infinispan이 클러스터 전체에 해당 데이터 전파**

```java
try {
    boolean acquired = lock.tryLock(10, java.util.concurrent.TimeUnit.SECONDS).get();
    if (acquired) {
        try {
            cache.putForExternalRead(key, value);
        } finally {
            lock.unlock();
        }
    } else {
        logger.warn("Could not acquire distributed lock for key: " + key);
    }
} catch (Exception e) {
    logger.error("Error during safePut for key: " + key, e);
}
```

**6\. 각 노드는 SessionCacheListener로 이벤트 수신**

```java
@CacheEntryCreated
public void sessionCreated(CacheEntryCreatedEvent<String, Object> event) {
    if (!event.isPre()) { // 이벤트가 실제로 발생한 후에만 처리
        String userId=event.getKey();
    }
}
```

### **5\. 세션 제거 흐름 (로그아웃, 타임아웃 등)**

**1\. 로그아웃 또는 세션 만료 발생**

```java
infinispanCacheService.remove("dist", reqInfo.getIntgAcntId());
```

**2\. InfinispanCacheService.remove() 호출로 캐시에서 제거**

```java
 try {
     boolean acquired = lock.tryLock(10, java.util.concurrent.TimeUnit.SECONDS).get();
     if (acquired) {
         try {
             cache.remove(key);
         } finally {
             lock.unlock();
         }
     } else {
         logger.warn("@@@@ Could not acquire distributed lock for key : " + key);
     }
 } catch (Exception e) {
     logger.error("@@@@ Error during safeRemove for key : " + key, e);
 }
```

**3\. Infinispan → @CacheEntryRemoved 이벤트 발생**

```java
@CacheEntryRemoved
public void sessionRemoved(CacheEntryRemovedEvent<String, Object> event) {
	..생략
}
```

**4\. SessionCacheListener에서 HttpSessionTracker 통해 invalidate()**

```java
try {
    HttpSession session = httpSessionTracker.getSession(sessionId);
    if (session != null) {
        session.invalidate();
    } else {
        logger.warn("Session not found for invalidation - userId: {}, sessionId: {}", userId, sessionId);
    }
} catch (IllegalStateException e) {
    logger.warn("Session already invalidated - userId: {}, sessionId: {}", userId, sessionId);
} catch (Exception e) {
    logger.error("Error during session invalidation - userId: {}, sessionId: {}", userId, sessionId, e);
}
```

### **6\. 핵심 소스 설명**

☑️ **LgnService (로그인 서비스)**

```java
HttpSession session = request.getSession(true);
session.setAttribute("USER_DETAIL", user);
infinispanCacheService.put(userId, session.getId());
```

☑️ **InfinispanCacheService**

-   **sessionId 저장/조회/삭제 수행**
-   **putForExternalRead, remove 사용**

☑️ **SessionCacheListener**

```java
@CacheEntryRemoved
public void sessionRemoved(...) {
    HttpSession session = sessionTracker.getSession(sessionId);
    session.invalidate();
}
```

**☑️ HttpSessionTracker**

-   **로컬 세션 메모리에서 sessionId → HttpSession 추적**
-   **HttpSessionListener를 통해 생성/삭제 감지**

### **7\. 캐시 이벤트 흐름 도식**

![](/assets/img/posts/318/2.png)

-   **put → @CacheEntryCreated**
-   **remove → @CacheEntryRemoved**

### **8\. 구조의 장단점**

#### 장점

-   **분산 환경에서도 세션 일관성 유지**
-   **서버가 여러 개여도 사용자 인증 상태 공유**
-   **캐시 기반으로 빠른 응답성과 확장성 확보**

#### 단점

-   **Infinispan 설정과 Spring 연동이 수동적 (자동화 미지원)**
-   **Spring Session처럼 추상화된 인터페이스 없음**
-   **유지보수 시 캐시 key, TTL, 이벤트 동기화 체크 필요**

### **9\. 마무리**

**이 구조는 Spring 단에서 직접 Infinispan 캐시에 세션 ID를 저장하고,**  
**클러스터 간 이벤트 수신을 통해 로그아웃이나 세션 타임아웃도 함께 동기화하는 방식.**

**Spring Session + Redis보다 복잡하지만,**  
**JBoss 환경에 익숙하고 대용량 인증 처리 성능이 중요할 경우 좋은 대안이 될 수 있다.**

* * *

**추가적으로, **HttpSessionTracker가 호출되는 시점****

```java
// 첫 로그인시, 세션 생성 부분
HttpSession session = request.getSession(true);
session.setAttribute("USER_DETAIL", user);
String sessionId=session.getId();

// 생성 시, 자동호출
@Override
public void sessionCreated(HttpSessionEvent se) {
    sessions.put(se.getSession().getId(), se.getSession());
}

// 로그아웃시, 세션 삭제 부분
session.invalidate();

// 삭제 시, 자동호출
@Override
public void sessionDestroyed(HttpSessionEvent se) {
    sessions.remove(se.getSession().getId());
}
```
