---
title: "[Spring & JBoss] 중복 로그인 감지 방법 비교: 나의 구현 vs 일반적인 방법"
date: 2025-07-24 15:06:26 +0900
categories: ["ETC", "IT Knowledge"]
tags: ["스프링 제이보스 중복로그인 처리", "중복 로그인 처리 방법"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-JBoss-%EC%A4%91%EB%B3%B5-%EB%A1%9C%EA%B7%B8%EC%9D%B8-%EA%B0%90%EC%A7%80-%EB%B0%A9%EB%B2%95-%EB%B9%84%EA%B5%90-%EB%82%98%EC%9D%98-%EA%B5%AC%ED%98%84-vs-%EC%9D%BC%EB%B0%98%EC%A0%81%EC%9D%B8-%EB%B0%A9%EB%B2%95"
---
#### **내가 만든 중복로그인 감지 방법 특징**

-   **메모리 기반 플래그와 캐시 이벤트 활용**  
    **\- 로그인 시 기존 세션 ID를 Infinispan 캐시에 저장하고, 중복 로그인 시 해당 키를 삭제하여(cache.remove)하여  
    이벤트 발생**  
    **\- 이벤트 리스너에서 삭제사유를 확인하여 중복 로그인 플래그를 별도로 저장**
-   **쿠키 복호화로 사용자 식별**  
    **\- 클라이언트가 보낸 암호화된 쿠키를 복호화하여 사용자 ID 추출 후, 중복 로그인 여부를 플래그 맵에서 확인**
-   **스프링 Bean으로 상태 공유와 통합 관리**  
    **\- 이벤트 리스너부터 서비스, 인증 진입점까지 스프링 ID로 동일 인스턴스의 메모리 맵을 공유 하여 상태 일관성 유지**
-   **외부 저장소 없이 자체 메모레 + Infinispan 캐시 이벤트만 활용**  
    **\- Resdis 같은 별도 분산 저장소 없이 세션 중복 관리를 구현**

**1\. 메모리 기반 플래그와 캐시 이벤트 활용**

```java
// 로그인시에 기존 캐시에 사용자가 있는지 조회하고 있으면 중복로그인 플래그에 저장

// 중복 로그인 감지: 기존 세션 아이디 조회
String existingSessionId  = (String) infinispanCacheService.get("aa", userMngLgnReqC.getIntgAcntId());
logger.info("중복로그인 체크 ID : {}", reqInfo.getIntgAcntId());
if (existingSessionId != null) { // 기존세션이 있다면 세션정지 이유 추가
    logger.info("existing User Session {}", reqInfo.getIntgAcntId());
    sessionCacheListener.getRemovalReasonMap().put(reqInfo.getIntgAcntId(), SessionInvalidationReason.DUPLICATE_LOGIN_SESSION.getCode());
}
```

```java
@Component
@Listener(clustered = true)  // Infinispan 캐시 이벤트 리스너 등록용 어노테이션
public class SessionCacheListener {

    // 중복 로그인 등 세션 종료 사유 저장용 맵 (메모리 기반)
    private final ConcurrentMap<String, String> removalReasonMap = new ConcurrentHashMap<>();

    // 중복 로그인 사용자 표시 플래그 맵
    private final ConcurrentMap<String, Boolean> duplicateLoginUserMap = new ConcurrentHashMap<>();

    // Getter - 로그인 서비드 등에서 상태 접근용
    public ConcurrentMap<String, String> getRemovalReasonMap() {
        return removalReasonMap;
    }
    public ConcurrentMap<String, Boolean> getDuplicateLoginUserMap() {
        return duplicateLoginUserMap;
    }

    @CacheEntryRemoved
    public void sessionRemoved(CacheEntryRemovedEvent<String, Object> event) {
 
 		... 생략
		
        // 로그인 서비스단에서 중복로그인으로 등록된 값을 가지고온다.
        String reason = removalReasonMap.remove(userId);

        if ("중복로그인사용자".equals(reason)) {
            logger.info("중복 로그인에 의한 세션 강제 종료 - userId: {}", userId);
            duplicateLoginUserMap.put(userId, true);
        } else {
            logger.info("일반 세션 종료 또는 타임아웃 - userId: {}", userId);
        }
        
        ... 생략

        session.invalidate();
        
        ... 생략
}
```

**2\. 쿠키 복호화로 사용자 식별**

```java
@Component
public class CustomAuthenticationEntryPoint implements AuthenticationEntryPoint {

    @Override
    public void commence(HttpServletRequest request, HttpServletResponse response,
                        AuthenticationException authException) throws IOException {

        // 기존 세션 만료와 중복 로그인 여부 구분
        String userId = extractUserIdFromNsdpCookie(request);

        boolean isDuplicateLogin = false;
        if (userId != null) {
            Boolean flag = sessionCacheListener.getDuplicateLoginUserMap().remove(userId);
            if (Boolean.TRUE.equals(flag)) {
                isDuplicateLogin = true;
            }
        }

        if (isDuplicateLogin) { // 중복로그인인 경우
            // 중복 로그인 세션 만료시의 전용 에러 처리
            exception = AuthException.duplicateLogin(); // 이 에러코드는 따로 정의해야 함
        } else { // 일반 세션만료
            ...
        }

    }
	
    private String extractUserIdFromNsdpCookie(HttpServletRequest request) {
	    Cookie[] cookies = request.getCookies();
	    if (cookies != null) {
	        for (Cookie cookie : cookies) {
	            if ("쿠키".equals(cookie.getName())) {
	                try {
	                    String decrypted = cookie.getValue();
	                    String[] parts = decrypted.split("/");
	                    if (parts.length > 0) {
	                        return parts[0]; // userId
	                    }
	                } catch (Exception e) {
	                    logger.warn("쿠키 복호화 실패", e);
	                }
	            }
	        }
	    }
	    return null;
	}
}
```

**3\. 스프링 Bean으로 상태 공유와 통합 관리 (InfinispanCacheService에서 빈 주입 및 리스너 등록)**

```java
@Service
@DependsOn("httpSessionTracker")
public class InfinispanCacheService {
    @Autowired
    private HttpSessionTracker httpSessionTracker;

    @Autowired
    private SessionCacheListener sessionCacheListener;  // *스프링 빈 주입*

    @PostConstruct
    public void init() {
        logger.info("InfinispanCacheService init - SessionCacheListener: " + sessionCacheListener);
        // 스프링이 관리하는 동일 빈 인스턴스를 Infinispan 캐시 리스너로 등록
        cacheManager.getCache("dist").addListener(sessionCacheListener);
    }
}
```

<table style="border-collapse: collapse; width: 100.464%; height: 93px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 21px;"><td style="width: 26.9767%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>기능</b></span></td><td style="width: 73.4884%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>주요 역할 및 위치</b></span></td></tr><tr style="height: 21px;"><td style="width: 26.9767%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>메모리 기반 플래그 관리</b></span></td><td style="width: 73.4884%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>SessionCacheListener<span style="background-color: oklch(0.9902 0.004 106.47); text-align: start;">&nbsp;내부의 ConcurrentMap 활용</span></b></span></td></tr><tr style="height: 17px;"><td style="width: 26.9767%; height: 17px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b><span style="background-color: oklch(0.9902 0.004 106.47); text-align: start;">캐시 이벤트 수신 및 세션 무효화</span></b></span></td><td style="width: 73.4884%; height: 17px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>SessionCacheListener.sessionRemoved()<span style="background-color: oklch(0.9902 0.004 106.47); text-align: start;">&nbsp;메소드</span></b></span></td></tr><tr style="height: 17px;"><td style="width: 26.9767%; height: 17px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b><span style="background-color: oklch(0.9902 0.004 106.47); text-align: start;">사용자 식별을 위한 쿠키 복호화</span></b></span></td><td style="width: 73.4884%; height: 17px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>CustomAuthenticationEntryPoint<span style="background-color: oklch(0.9902 0.004 106.47); text-align: start;">&nbsp;내 쿠키 복호화 로직</span></b></span></td></tr><tr style="height: 17px;"><td style="width: 26.9767%; height: 17px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b><span style="background-color: oklch(0.9902 0.004 106.47); text-align: start;">상태 공유 및 싱글톤 빈 활용</span></b></span></td><td style="width: 73.4884%; height: 17px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>SessionCacheListener<span style="background-color: oklch(0.9902 0.004 106.47); text-align: start;">와&nbsp;</span>InfinispanCacheService<span style="background-color: oklch(0.9902 0.004 106.47); text-align: start;">에&nbsp;</span>@Component/@Service<span style="background-color: oklch(0.9902 0.004 106.47); text-align: start;">와 DI 활용</span></b></span></td></tr></tbody></table>

* * *

#### **일반적인 중복 로그인 감지 및 처리 방법**

<table style="border-collapse: collapse; width: 101.047%; height: 232px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 21px;"><td style="width: 23.2506%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>방법 유형</b></span></td><td style="width: 48.765%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>설명</b></span></td><td style="width: 29.0437%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>장/단점</b></span></td></tr><tr style="height: 21px;"><td style="width: 23.2506%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>단순 세션 관리</b></span></td><td style="width: 48.765%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>세션을 전역 Map 또는 컬렉션에 저장 후 중복 감지 및 무효화</b></span></td><td style="width: 29.0437%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>구현 간단, 분산환경 지원 어려움</b></span></td></tr><tr style="height: 42px;"><td style="width: 23.2506%; height: 42px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>HttpSessionListener</b></span></td><td style="width: 48.765%; height: 42px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>세션 생성/만료 이벤트로 중복 로그인 감지</b></span></td><td style="width: 29.0437%; height: 42px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>서버 내 세션 모니터링 용이,</b></span><br><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>분산환경 시 한계</b></span></td></tr><tr style="height: 42px;"><td style="width: 23.2506%; height: 42px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>Spring Security 내장 기능</b></span></td><td style="width: 48.765%; height: 42px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>최대 세션 수 제한 기능 활용</b></span></td><td style="width: 29.0437%; height: 42px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>프레임워크 기능 활용 편리,</b></span><br><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>UI 메시지 추가 구현 필요</b></span></td></tr><tr style="height: 64px;"><td style="width: 23.2506%; height: 64px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>JWT 토큰 기반</b></span></td><td style="width: 48.765%; height: 64px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>무상태 인증, 토큰 관리로 중복 로그인 제어</b></span></td><td style="width: 29.0437%; height: 64px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>확장성 좋음,</b></span><br><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>토큰 즉시 무효화 어렵고 관리 복잡</b></span></td></tr><tr style="height: 42px;"><td style="width: 23.2506%; height: 42px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>Redis 등 분산 저장소 활용</b></span></td><td style="width: 48.765%; height: 42px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>세션 상태나 로그인 상태를 Redis에 저장, 서버간 동기화</b></span></td><td style="width: 29.0437%; height: 42px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>분산환경 대응 가능,</b></span><br><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>인프라 관리 및 비용 발생</b></span></td></tr></tbody></table>

**주요 차이점 정리**

<table style="border-collapse: collapse; width: 100%; height: 185px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 21px;"><td style="width: 26.938%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>구분</b></span></td><td style="width: 39.7286%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>내가 구현한 방식</b></span></td><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>일반적인 방법</b></span></td></tr><tr style="height: 21px;"><td style="width: 26.938%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>상태 저장소</b></span></td><td style="width: 39.7286%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>Infinispan 캐시 + 메모리 기반 플래그</b></span></td><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>세션, 외부 캐시(Redis), 토큰, DB 등</b></span></td></tr><tr style="height: 21px;"><td style="width: 26.938%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>분산 환경 지원</b></span></td><td style="width: 39.7286%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b><span style="text-align: start;">Infinispan&nbsp; 이벤트 리스너 활용한 분산 이벤트 감지</span></b></span></td><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>Redis 등 외부 저장소 활용이 일반적</b></span></td></tr><tr style="height: 42px;"><td style="width: 26.938%; height: 42px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>인증 실패 시 중복로그인 알림</b></span></td><td style="width: 39.7286%; height: 42px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>인증 실패 시 쿠키 복호화 후 플래그 확인, 별도 중복 로그인 에러 반환</b></span></td><td style="width: 33.3333%; height: 42px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>대부분 프레임워크 기본 메시지 또는<br>커스텀 구현 필요</b></span></td></tr><tr style="height: 42px;"><td style="width: 26.938%; height: 42px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>외부 의존성</b></span></td><td style="width: 39.7286%; height: 42px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>없음( <span style="text-align: start;">Infinispan 캐시 제외, Redis 등 미사용)</span></b></span></td><td style="width: 33.3333%; height: 42px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>Redis, DB, 토큰 관리 서버 등 인프라<br>추가 필요</b></span></td></tr><tr style="height: 21px;"><td style="width: 26.938%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>확장성 및 관리 편의성</b></span></td><td style="width: 39.7286%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>스프링 DI 활용해 상태 공유, 메모리 관리 집중</b></span></td><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>외부 인프라 복잡성 있지만 규모 확장<br>유리</b></span></td></tr><tr style="height: 17px;"><td style="width: 26.938%; height: 17px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>사용자 경험</b></span></td><td style="width: 39.7286%; height: 17px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>명확한 중복 로그인 에러 메시지 전송 가능</b></span></td><td style="width: 33.3333%; height: 17px;"><span style="font-family: 'Noto Serif KR'; color: #333333;"><b>메시지 커스터마이징 별도 구현 필요</b></span></td></tr></tbody></table>

**정리**

-   **내가 구현한 코드는 메모리 기반 처리와 Infinispan 이벤트를 이용하여 외부 인프라 부담 없이 구현한 사례**
-   **일반적인 방법들은 인프라(Redis, 토큰, DB) 의존도가 높고, 분산 환경 대응에 더 적합하지만 복잡성 증가 발생**
-   **구축 환경, 운영 규모, 안정성 요구에 따라 가장 적합한 방식을 선택해서 사용 필요**
