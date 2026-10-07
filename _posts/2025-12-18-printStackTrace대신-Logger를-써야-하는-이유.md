---
title: "printStackTrace()대신 Logger를 써야 하는 이유"
date: 2025-12-18 13:02:42 +0900
categories: ["ETC", "IT Knowledge"]
tags: ["java printstacktrace", "printstacktrace vs exception", "printstacktrace 예외처리", "printstacktrace란?", "예외처리 방법", "자바 예외처리"]
tistory_url: "https://aroma-bok.tistory.com/entry/printStackTrace%EB%8C%80%EC%8B%A0-Logger%EB%A5%BC-%EC%8D%A8%EC%95%BC-%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0"
---
**개발을 진행하면서 예외처리에 대한 관심이 더 생기고 조금 더 깔끔하게 코드를 짜고 싶은 욕심이 생겨서 정리해 보았다.**

* * *

#### **printStackTrace()란?**

-   **예외(Exception) 객체가 가진 스택 트레이스를 표준 에러 출력(stderr)으로 그대로 찍어주는 메서드**

```java
try {
    int a = 10 / 0;
} catch (Exception e) {
    e.printStackTrace();
}
```

```java
java.lang.ArithmeticException: / by zero
    at com.example.Test.main(Test.java:10)
```

**특징**

-   **어디서 발생했는지 즉시 확인 가능**
-   **설정 필요 없음**
-   **무조건 콘솔에 출력**

**단점**

-   **로그 레벨 개념 없음**
-   **파일로 남기기 어려움**
-   **운영 환경에서 로그 관리 불가**
-   **로그 포맷 통일 불가**
-   **로그 수집(APM, ELK 등)에 안 잡히는 경우 많음**
-   **즉, 디버깅용 임시 출력용**

* * *

#### **Logger (log4j 등)란?**

-   **애플리케이션의 로그를 체계적으로 기록하기 위한 프레임워크**
-   **예외뿐 아니라, 흐름, 상태, 경고, 오류 전부 기록**

```java
private static final Logger logger = LogManager.getLogger(Test.class);

try {
    int a = 10 / 0;
} catch (Exception e) {
    logger.error("계산 중 오류 발생", e);
}
```

```java
2025-12-15 17:43:05 ERROR [Test] 계산 중 오류 발생
java.lang.ArithmeticException: / by zero
    at com.example.Test.main(Test.java:10)
```

**장점**

-   **로그 레벨 관리 기능(DEBUG, INFO, WARN, ERROR)**
-   **파일, 콘솔, DB 등 다양한 출력 가능**
-   **로그 롤링(용량 관리)**
-   **운영 환경에 최적화**
-   **ELK, Scouter, CloudWatch 등 연동 가능**

<table style="border-collapse: collapse; width: 100%; height: 176px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 22px;"><td style="width: 21.7054%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>구분</b></span></td><td style="width: 37.2867%; height: 22px;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>printStackTrace()</b></span></td><td style="width: 41.0078%; height: 22px;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>Logger (log4j)</b></span></td></tr><tr style="height: 22px;"><td style="width: 21.7054%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>목적</b></span></td><td style="width: 37.2867%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>즉석 디버깅</b></span></td><td style="width: 41.0078%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>운영/분석 로그</b></span></td></tr><tr style="height: 22px;"><td style="width: 21.7054%; height: 22px;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>출력 위치</b></span></td><td style="width: 37.2867%; height: 22px;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>콘솔(stderr)</b></span></td><td style="width: 41.0078%; height: 22px;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>콘솔/파일/외부</b></span></td></tr><tr style="height: 22px;"><td style="width: 21.7054%; height: 22px;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>로그 레벨</b></span></td><td style="width: 37.2867%; height: 22px;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>X 없음</b></span></td><td style="width: 41.0078%; height: 22px;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>O 있음</b></span></td></tr><tr style="height: 22px;"><td style="width: 21.7054%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>포맷</b></span></td><td style="width: 37.2867%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>고정</b></span></td><td style="width: 41.0078%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>커스터마이징</b></span></td></tr><tr style="height: 22px;"><td style="width: 21.7054%; height: 22px;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>관리</b></span></td><td style="width: 37.2867%; height: 22px;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>불가능</b></span></td><td style="width: 41.0078%; height: 22px;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>가능</b></span></td></tr><tr style="height: 22px;"><td style="width: 21.7054%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>운영 사용</b></span></td><td style="width: 37.2867%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>X</b></span></td><td style="width: 41.0078%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>O</b></span></td></tr><tr style="height: 22px;"><td style="width: 21.7054%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>성능 고려</b></span></td><td style="width: 37.2867%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>X</b></span></td><td style="width: 41.0078%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>O</b></span></td></tr></tbody></table>

**printStackTrace()는 개발 중 즉시 확인용이고,**

**Logger는 운영 환경에서 로그를 관리·분석하기 위한 표준 방식이다.**  
**운영 코드에서는 반드시 Logger를 사용해야 한다.**

* * *

#### **실무에서의 사용**

****●** 이렇게 하면 안 됨 (운영 기준)**

```java
catch (Exception e) {
    e.printStackTrace();
}
```

-   **로그 파일에 안 남음**
-   **장애 분석 불가**
-   **보안 로그 통제 불가**

****●** 이렇게 해야 함 (정석)**

```java
catch (Exception e) {
    logger.error("회원가입 처리 중 오류", e);
}
```

-   **메시지 + 스택 트레이스**
-   **로그 레벨 분리**
-   **운영 추적 가능**

* * *

#### ***그럼 printStackTrace()는 언제 써도 되나??***

****●** 허용되는 경우**

-   **로컬 테스트**
-   **임시 디버깅**
-   **알고리즘 문제 풀이**
-   **로그 프레임워크 없는 단순 코드**

****●** 쓰면 안 되는 경우**

-   **서버 코드**
-   **배치**
-   **API**
-   **운영 환경**

* * *

#### **참고**

****●** logger.error(e) vs logger.error("msg", e)**

```java
logger.error(e);          // ❌ 비추천
logger.error("에러 발생", e); // ✅ 추천
```

-   **메시지가 없으면 로그 검색이 어려움**

* * *

### **내가 착각했던 부분**

****●** 왜 JBoss에서는 printStackTrace()가 로그처럼 보일까?**

**→ JBoss를 이용하면 printStackTrace()도 로그에 찍혀서 정상 작동이 되고 있다고 착각**

**1\. printStackTrace()는 내부적으로 System.err로 출력**

```java
e.printStackTrace();  // → System.err
```

**2\. JBoss(WildFly) 기본 작동 방식**

```java
System.out / System.err
    ↓
JBoss Logging
    ↓
server.log
```

**표준 출력/에러를 로그 파일로 리다이렉트 하고 있음**

**그래서 printStackTrace()를 사용해도 server.log에 찍히는 것처럼 보이는 것**

**하지만 로그백(Appender)과는 "완전히 다른 경로"**

**● 프로젝트 로그백 설정**

```java
<Appenders>
    <Console name="console" target="SYSTEM_OUT">
        <PatternLayout ... />
    </Console>
</Appenders>
```

**Appender는 Logger가 남긴 로그만 제어한다. printStackTrace()는 로그 프레임워크를 거치지 않는다.**

* * *

#### **★ 이로 인해 생긴 문제 **★****

**1\. 로그 포맷이 깨짐**

```java
-printStackTrace()
2025-12-16 11:25:28,967 [34mDEBUG[m [  XNIO-1 task-1] [36mk.g.s.p.d.c.l.m.L.getPasswordErrorCount [m : <==      Total: 1
kr.go.test.cmm.advice.exception.InvalidPasswordException
	at kr.go.test.paa.config.session.CustomAuthenticationProvider.authenticate(CustomAuthenticationProvider.java:127)
	at org.springframework.security.authentication.ProviderManager.authenticate(ProviderManager.java:182)
	at org.springframework.security.authentication.ProviderManager.authenticate(ProviderManager.java:201)
	at org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter$AuthenticationManagerDelegator.authenticate(WebSecurityConfigurerAdapter.java:531)
	at kr.go.test.paa.domain.com.lgn.service.LgnService.login(LgnService.java:128)

vs

-Logger
2025-12-16 11:27:06,162 [33m WARN[m [  XNIO-1 task-1] [36mk.g.s.p.d.c.l.s.LgnService              [m : 사용자 비밀번호 실패: id=test
kr.go.test.cmm.advice.exception.InvalidPasswordException
	at kr.go.test.paa.config.session.CustomAuthenticationProvider.authenticate(CustomAuthenticationProvider.java:127)
	at org.springframework.security.authentication.ProviderManager.authenticate(ProviderManager.java:182)
	at org.springframework.security.authentication.ProviderManager.authenticate(ProviderManager.java:201)
	at org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter$AuthenticationManagerDelegator.authenticate(WebSecurityConfigurerAdapter.java:539)
	at kr.go.test.paa.domain.com.lgn.service.LgnService.login(LgnService.java:128)
```

**2\. 로그 레벨 제어 불가**

```java
<Root level="ERROR">
```

**이렇게 설정해도 printStackTrace()는 무조건 출력됨 (장애 상황에서 로그 폭탄)**

**3\. 로그 롤링/분리 불가**

-   **에러 로그 파일 분리 X**
-   **서비스별 로그 분리 X**
-   **하루 단위 관리 X**

**전부 Logger 기준이다.**

**4\. APM / ELK 연동 실패**

-   **Scouter**
-   **ELK**
-   **CloudWatch**
-   **Splunk**

**대부분 Logger 기반 수집이다. printStackTrace()는 수집이 안되거나, 한 줄 메시지로 깨짐**

**5\. 파일로 남는다 ≠ 로그로 남는다 **★****

-   **printStackTrace()는 로그 파일에 찍힐 수는 있지만 로그로 관리되지 않는다.**

* * *

#### **나의 착각을 정리하면**

-   **"겉보기 로그 결과"는 거의 같아 보이는 게 정상이라고 생각했다.**
-   **하지만 의미·통제·운영 관점에서는 확실히 다르다**

****●** 차이가 없어 보이는 이유는?**

-   **JBoss**
-   **Logback / Log4j2 설정**
-   **콘솔 로그가 서버 로그 파일로 리다이렉트**
-   **예외가 결국 WAS 로그에 찍힘**

**그래서 e.printStackTrace(); 도 결과적으로는 아래와 같은 상황이다.**

-   **stderr → JBoss 콘솔 → 로그 파일**
-   **스택 트레이스가 로그처럼 보이게 출력됨**

**하지만,** 

-   **로그 레벨 개념 X**
-   **로그 정책 X**
-   **메시지 구조 X**

**스택 트레이스는 부가 정보고 로그의 본질은 '의미 + 레벨 + 맥락'이다.**

* * *

#### **로그로 보는 차이점 파악**

```java
kr.go.test.cmm.advice.exception.InvalidPasswordException
// 누가 찍었는지 없음

vs

k.g.s.p.d.c.l.s.LgnService : 사용자 비밀번호 실패: id=test
// 어느 클래스 + 어떤 상황인지 명확
```

#### **실무 기준 패턴 정리**

******●**** 안 좋은 패턴**

```java
catch (Exception e) {
    e.printStackTrace();
    throw e;
}
```

******●**** 좋은 패턴**

```java
catch (InvalidPasswordException e) {
    log.warn("사용자 비밀번호 실패: id={}", userId, e);
    throw e;
}
```

******●**** 더 좋은 패턴**

```java
catch (InvalidPasswordException e) {
    throw e; // 로깅은 GlobalExceptionHandler에서
}
```
