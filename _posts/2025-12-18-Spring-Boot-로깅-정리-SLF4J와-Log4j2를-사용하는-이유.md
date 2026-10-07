---
title: "Spring Boot 로깅 정리 - SLF4J와 Log4j2를 사용하는 이유"
date: 2025-12-18 17:19:32 +0900
categories: ["ETC", "IT Knowledge"]
tags: ["log4j2 logback 차이", "slf4j log4j2", "slf4j log4j2 차이", "spring boot 예외처리", "스프링 부트 예외처리 방법", "스프링 예외처리", "예외처리방법", "자바 예외라이브러리", "자바 예외처리"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Boot-%EB%A1%9C%EA%B9%85-%EC%A0%95%EB%A6%AC-SLF4J%EC%99%80-Log4j2%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0"
---
**개발을 진행하면서 log4j / slf4j / Logback 을 왜 사용하는지 그리고 각각의 개념을 정리했다.**

* * *

![](/assets/img/posts/330/1.png)

#### **SLF4J (Simple Logging Facade for Java)**

-   **로깅 파사드(Facade)**
-   **실제 로그 구현체가 아니라 인터페이스(추상화 계층)**
-   **로깅 구현체(Log4J, Logback 등)를 느슨하게 연결**
-   **로깅 API의 표준 인터페이스 역할**

****●** 구조**

```java
[ Application ]
      ↓
   SLF4J - 표준 인터페이스
      ↓
[ Logback | Log4J | Log4J2 ] - 구현체
```

**● Lombok 사용 안하는 방법**

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class SampleService {
    private static final Logger log = LoggerFactory.getLogger(SampleService.class);

    public void test() {
        log.info("info log");
        log.error("error log");
    }
}
```

****●** Lombok 사용하는 방법**

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

@Slf4j
public class SampleService {

    public void test() {
        log.info("info log");
        log.error("error log");
    }
}
```

**장점**

-   **구현체 교체가 쉬움**
-   **코드 변경 없이 로깅 프레임워크 교체 가능**
-   **파라미터 바인딩 지원 → 성능 우수**  
    **∴ log.debug("userId={}, name={}", userId, name);**

**단점**

-   **단독으로는 로그 출력 불가**
-   **반드시 구현체 필요**

* * *

![](/assets/img/posts/330/2.png)

#### **Log4J2 (Apache Log4J2)**

-   **Apache에서 만든 로깅 구현체**
-   **예전부터 많이 사용됨**
-   **Log4J 1.x는 현재 EQL(사용 비권장)**

****●** 예시 코드 (Log4J 1.x) - Log4J (단독 사용방법)**

```java
import org.apache.log4j.Logger;

public class Sample {
    static Logger logger = Logger.getLogger(Sample.class);

    public static void main(String[] args) {
        logger.info("Hello Log4J");
    }
}
```

****●** 예시 코드 (Log4J 2.x) - Log4J2 (단독 사용방법)**

```java
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

public class Log4j2Example {

    private static final Logger logger =
            LogManager.getLogger(Log4j2Example.class);

    public void test() {
        logger.info("Log4j2 logging");
    }
}
```

****●** 설정 파일 (log4j2.xml)**

```java
<Configuration>
    <Appenders>
        <Console name="Console">
            <PatternLayout
                pattern="%d{yyyy-MM-dd HH:mm:ss} %-5level %logger - %msg%n"/>
        </Console>
    </Appenders>

    <Loggers>
        <Root level="INFO">
            <AppenderRef ref="Console"/>
        </Root>
    </Loggers>
</Configuration>
```

****●** 의존성 주입**

```java
<!-- SLF4J API (인터페이스) -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
</dependency>

<!-- Log4j2 구현체 + SLF4J 브릿지 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>

<!-- 반드시 제외 [Logback과 충돌] -->
<exclusion>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-logging</artifactId>
</exclusion>
```

**장점**

-   **비동기 로깅 지원 (Async Logger)**
-   **성능 매우 우수**
-   **Log4Shell 이후 보안 강화**
-   **유연한 설정 (XML / JSON / YAML)**

**단점**

-   **설정이 상대적으로 복잡**
-   **SLF4J 없이 직접 사용 시 종속성 증가**

* * *

![](/assets/img/posts/330/3.png)

#### **Logback**

-   **SLF4 개발자가 만든 로깅 구현체**
-   **Spring Boot 기본 로깅 프레임워크**
-   **안정성과 단순한 중심**

****●** 예시 코드**

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class LogbackExample {

    private static final Logger log =
            LoggerFactory.getLogger(LogbackExample.class);

    public void test() {
        log.info("Logback logging");
    }
}
```

****●** 설정 파일(logback.xml)**

```java
<configuration>
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d %-5level %logger - %msg%n</pattern>
        </encoder>
    </appender>

    <root level="INFO">
        <appender-ref ref="STDOUT"/>
    </root>
</configuration>
```

****●** 의존성 주입**

```java
<!-- SLF4J + Logback (Spring Boot 기본) -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

**장점**

-   **설정 단순**
-   **Spring Boot와 궁합 좋음**
-   **안정적**

**단점**

-   **Log4j2 대비 고급 기능 부족**
-   **비동기 처리 성능은 상대적으로 낮음**

* * *

**차이점**

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft"><tbody><tr><td style="width: 25%;"><span style="font-family: 'Noto Serif KR';"><b>구분</b></span></td><td style="width: 25%;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>SLF4J</b></span></td><td style="width: 25%;"><span style="font-family: 'Noto Serif KR'; color: #006dd7;"><b>Log4j2</b></span></td><td style="width: 25%;"><span style="font-family: 'Noto Serif KR'; color: #006dd7;"><b>Logback</b></span></td></tr><tr><td style="width: 25%;"><span style="font-family: 'Noto Serif KR';"><b>역할</b></span></td><td style="width: 25%;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>인터페이스</b></span></td><td style="width: 25%;"><span style="font-family: 'Noto Serif KR'; color: #006dd7;"><b>구현체</b></span></td><td style="width: 25%;"><span style="font-family: 'Noto Serif KR'; color: #006dd7;"><b>구현체</b></span></td></tr><tr><td style="width: 25%;"><span style="font-family: 'Noto Serif KR';"><b>단독 사용</b></span></td><td style="width: 25%;"><span style="font-family: 'Noto Serif KR';"><b>X</b></span></td><td style="width: 25%;"><span style="font-family: 'Noto Serif KR';"><b>O</b></span></td><td style="width: 25%;"><span style="font-family: 'Noto Serif KR';"><b>O</b></span></td></tr><tr><td style="width: 25%;"><span style="font-family: 'Noto Serif KR';"><b>성능</b></span></td><td style="width: 25%;"><span style="font-family: 'Noto Serif KR';"><b>-</b></span></td><td style="width: 25%;"><span style="font-family: 'Noto Serif KR';"><b>우수</b></span></td><td style="width: 25%;"><span style="font-family: 'Noto Serif KR';"><b>비교적 낮음</b></span></td></tr><tr><td style="width: 25%;"><span style="font-family: 'Noto Serif KR';"><b>비동기</b></span></td><td style="width: 25%;"><span style="font-family: 'Noto Serif KR';"><b>-</b></span></td><td style="width: 25%;"><span style="font-family: 'Noto Serif KR';"><b>O</b></span></td><td style="width: 25%;"><span style="font-family: 'Noto Serif KR';"><b>제한적</b></span></td></tr><tr><td style="width: 25%;"><span style="font-family: 'Noto Serif KR';"><b>Spring Boot</b></span></td><td style="width: 25%;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>필수</b></span></td><td style="width: 25%;"><span style="font-family: 'Noto Serif KR'; color: #006dd7;"><b>선택</b></span></td><td style="width: 25%;"><span style="font-family: 'Noto Serif KR'; color: #006dd7;"><b>기본</b></span></td></tr><tr><td style="width: 25%;"><span style="font-family: 'Noto Serif KR';"><b>보안</b></span></td><td style="width: 25%;"><span style="font-family: 'Noto Serif KR';"><b>안전</b></span></td><td style="width: 25%;"><span style="font-family: 'Noto Serif KR';"><b>강화됨</b></span></td><td style="width: 25%;"><span style="font-family: 'Noto Serif KR';"><b>안전</b></span></td></tr></tbody></table>

* * *

#### **실무 권장 조합**

****●** 가장 권장**

```java
SLF4J + Log4j2
```

****●** Spring Boot 기본**

```java
SLF4J + Logback
```

**→ Log4j2 / Logback을 동시에 쓰는 건 X**

**SLF4J는 로깅 표준 인터페이스이며, Log4J2와 Logback은 실제 로그를 처리하는 구현체**

* * *

#### ****★** SLF4J를 통한 로깅을 사용해야만 하는 이유 ★**

**방법 1. Lombok @Slf4j 사용**

```java
import lombok.extern.slf4j.Slf4j;

@Slf4j
@Service
public class MemberService {

    public void join(String userId) {
        log.info("회원 가입 요청 userId={}", userId);
    }
}
```

**방법 2. LoggerFactory 직접 사용**

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

@Service
public class MemberService {

    private static final Logger log =
        LoggerFactory.getLogger(MemberService.class);

    public void join(String userId) {
        log.info("회원 가입 요청 userId={}", userId);
    }
}
```

**1\. 구현체 교체가 가능**

**● SLF4j를 이용하지 않음**

```java
LogManager.getLogger(...)
```

-   **Log4j2 → Logback 변경 시, 모든 소스 수정 필요**

****●** SLF4j를 이용**

```java
LoggerFactory.getLogger(...)
```

-   **pom.xml만 변경 (구현체 변경)**
-   **설정과 구현을 분리하는 구조**

* * *

**2\. 코드와 로깅 프레임워크의 결합도가 낮아짐**

****●** SLF4j를 이용하지 않음**

```java
import org.apache.logging.log4j.Logger;
```

-   **코드가 특정 프레임워크에 강하게 종속**

****●** SLF4j를 이용**

```java
import org.slf4j.Logger;
```

-   **인터페이스에만 의존**
-   **OCP(개방·폐쇄 원칙) 충족**
-   **유지보수·확장성에서 압도적 차이**

* * *

**3\. Spring Boot 및 주요 프레임워크와의 호환성**

-   **Spring Fraework**
-   **Spring Security**
-   **Hibernate**
-   **MyBatis**
-   **Kafka, Netty 등**

**→ 전부 SLF4J 기반**

> **SLF4J를 통한 로깅을 사용하면 로깅 구현체와의 결합도를 낮추고, 유지보수성과 확장성을 향상시킬 수 있다.**

* * *

#### **전체 조합 요약표**

<table style="border-collapse: collapse; width: 100%; height: 92px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 22px;"><td style="width: 20%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>사용 방식</b></span></td><td style="width: 20%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>SLF4J</b></span></td><td style="width: 20%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>Log4j2</b></span></td><td style="width: 20%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>Logback</b></span></td><td style="width: 20%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>권장</b></span></td></tr><tr style="height: 16px;"><td style="width: 20%; height: 16px;"><span style="font-family: 'Noto Serif KR';"><b>SLF4J + Log4j2</b></span></td><td style="width: 20%; height: 16px;"><span style="font-family: 'Noto Serif KR';"><b>●</b></span></td><td style="width: 20%; height: 16px;"><span style="font-family: 'Noto Serif KR';"><b>●</b></span></td><td style="width: 20%; height: 16px;">&nbsp;</td><td style="width: 20%; height: 16px;"><span style="font-family: 'Noto Serif KR';"><b>★ ★ ★</b></span></td></tr><tr style="height: 16px;"><td style="width: 20%; height: 16px;"><span style="font-family: 'Noto Serif KR';"><b>SLF4J + Logback &nbsp;</b></span></td><td style="width: 20%; height: 16px;"><span style="font-family: 'Noto Serif KR';"><b>●</b></span></td><td style="width: 20%; height: 16px;">&nbsp;</td><td style="width: 20%; height: 16px;"><span style="font-family: 'Noto Serif KR';"><b>●</b></span></td><td style="width: 20%; height: 16px;"><span style="font-family: 'Noto Serif KR';"><b>★ ★</b></span></td></tr><tr style="height: 22px;"><td style="width: 20%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>Log4j2 단독</b></span></td><td style="width: 20%; height: 22px;">&nbsp;</td><td style="width: 20%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>●</b></span></td><td style="width: 20%; height: 22px;">&nbsp;</td><td style="width: 20%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>비권장</b></span></td></tr><tr style="height: 16px;"><td style="width: 20%; height: 16px;"><span style="font-family: 'Noto Serif KR';"><b><span style="color: #333333; text-align: start;">Log4j2 + Logback</span></b></span></td><td style="width: 20%; height: 16px;">&nbsp;</td><td style="width: 20%; height: 16px;"><span style="font-family: 'Noto Serif KR';"><b>●</b></span></td><td style="width: 20%; height: 16px;"><span style="font-family: 'Noto Serif KR';"><b>●</b></span></td><td style="width: 20%; height: 16px;"><span style="font-family: 'Noto Serif KR';"><b>X</b></span></td></tr></tbody></table>
