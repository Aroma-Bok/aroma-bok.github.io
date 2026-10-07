---
title: "[Web] 웹 개발에서의 CORS(Cross-Origin Resource Sharing)와 HTTP 요청/응답 헤더, 그리고 Spring 기반 서버의 CORS 처리방식"
date: 2025-06-02 14:33:31 +0900
categories: ["Solution & Tools", "Web"]
tags: ["cors", "cors csrf 차이점", "csrf", "스프링 cors"]
tistory_url: "https://aroma-bok.tistory.com/entry/Web-%EC%9B%B9-%EA%B0%9C%EB%B0%9C%EC%97%90%EC%84%9C%EC%9D%98-CORSCross-Origin-Resource-Sharing%EC%99%80-HTTP-%EC%9A%94%EC%B2%AD%EC%9D%91%EB%8B%B5-%ED%97%A4%EB%8D%94-%EA%B7%B8%EB%A6%AC%EA%B3%A0-Spring-%EA%B8%B0%EB%B0%98-%EC%84%9C%EB%B2%84%EC%9D%98-CORS-%EC%B2%98%EB%A6%AC%EB%B0%A9%EC%8B%9D"
---
**CORS(Cross-Origin Resource Sharing)**  

-   **웹 브라우저에서 보안상 서로 다른 출처(Origin) 간의 리소스 공유를 제한하는 정책**
-   **서버가 명시적으로 허용하지 않으면, 브라우저가 다른 도메인/포트/프로토콜의 요청을 차단**

**HTTP 요청/응답 헤더**  

-   **요청(Request)과 응답(Response)에서 다양한 정보를 담는 메타데이터**

**CORS와 관련된 헤더**  

-   **요청 : Origin, Access-Control-Request-Method, Access-Control-Request-Headers 등**
-   **응답 : Access-Control-Allow-Origin, Access-Control-Allow-Methods, Access-Control-Allow-Headers 등**

**CORS의 동작 원리**  

-   **SOP(Same-Origin Policy, 동일 출처 정책) : 브라우저의 기본 보안 정책, 서로 다른 출처 간의 접근 제한**
-   **CORS(Cross-Origin Resource Sharing): SOP의 예외를 허용하는 메커니즘. 서버가 응답 헤더로  
    허용 출처/메서드/헤더 명시**

**실제 코드 예시**

```java
configuration.setAllowedOriginPatterns(getAllowedOrigins());

configuration.setAllowedMethods(Arrays.asList("POST"));

configuration.setAllowCredentials(true);

configuration.setAllowedHeaders(Collections.unmodifiableList(
        new ArrayList<>(Arrays.asList("Authorization"
                ,"Content-Type"
                ,"Accept"
                ,"Content-Disposition"
                ,"Content-Range"
                ,"Referer"
                ,"User-Agent"))
        ));

// 기존에는 테스트를 위해 아래를 사용했었음.
// configuration.setAllowedHeaders(Arrays.asList("*"));
```

> **요청 헤더란?**  
>   
> **요청 헤더는 클라이언트(브라우저 등)가 서버에 요청을 보낼 때, 요청의 성격이나 부가 정보를 담아 함께 전달하는**  
> **데이터**  
>   
> **예시: Content-Type, Accept, Authorization, User-Agent 등**  
> **브라우저는 CORS 상황에서 요청 헤더에 Origin(요청을 보낸 출처)도 자동으로 추가**

> **서버에서의 CORS 처리란?**  
>   
> **Spring WebConfig, CorsConfiguration, WebMvcConfigurer 등으로 CORS 정책을 설정하는 방법**  
> **allowedOrigins, allowedMethdos, allowedHeaders 등 세부 옵션의 의미와 역할**  
>   
> **CORS(Cross-Origin Resource Sharing)는 보안상의 이유로 브라우저가 다른 출처(도메인, 포트, 프로토콜)의**  
> **서버로 요청을 보낼 때, 서버가 명시적으로 허용하지 않으면 브라우저가 응답을 차단하는 정책**  
> **서버는 응답 헤더에 다음과 같은 CORS 관련 정보를 포함해 브라우저에 전달합니다.**  
>   
> **Access-Control-Allow-Origin: 허용할 오리진(출처) 지정**  
> **Access-Control-Allow-Methods: 허용할 HTTP 메서드 지정**  
> **Access-Control-Allow-Headers: 허용할 요청 헤더 지정**  
>   
> **브라우저는 서버 응답의 CORS 헤더를 검사해, 허용된 경우에만 실제 응답 데이터를 자바스크립트에서 사용**  
> **할 수 있도록 허용**

**프리플라이트(Preflight) 요청**  

-   **요청이 단순(Simple) 하지 않거나, 커스텀 헤더를 포함하거나, 특수한 Content-Type을 사용할 때 브라우저는 OPTIONS** **메서드로 프리플라이트 요청을 먼저 요청**
-   **이 요청에서, 브라우저는 실제 요청에서 사용할 메서드와 헤더를 서버에 미리 알리고, 서버가 허용하는지 확인**
-   **서버는 Access-Control-Allow-Headers에 실제 사용할 헤더들을 명시적으로 포함해야 하며,   
    그렇지 않으면 브라우저가 본 요청을 차단**

**요청-응답 흐름 예시**  

1.  **클라이언트가 요청을 보낼 때 여러 헤더(예: Content-Type, Accept, Content-Disposition 등)를 포함**
2.  **브라우저는 CORS 정책에 따라, 필요할 경우 프리플라이트(OPTIONS) 요청을 먼저 보냄**
3.  **서버는 응답 헤더에 허용할 오리진, 메서드, 헤더를 명시**
4.  **브라우저가 서버의 응답 헤더를 검사해 허용된 경우에만 실제 요청을 전송하고, 응답을 자바스크립트에서 사용할 수 있게 함**

#### **정리**

**클라이언트가 보내는 요청 헤더는 서버에서 Access-Control-Allow-Headers로 명시적으로 허용해야 하**

**며, 서버는 응답 헤더에 어떤 오리진, 메서드, 헤더를 허용할지 명확히 지정해야 브라우저가 정상적으로 요청**

**과 응답을 처리할 수 있습니다.**  

**CORS 정책은 브라우저와 서버가 HTTP 헤더를 통해 서로의 신뢰를 확인하고, 보안을 유지하면서 교차 출처  
자****원 접근을 허용하는 메커니즘입니다**

* * *

**Q. 실제로 프리플라이트(OPTIONS) 요청을 명시적으로 CORS의 setAllowedMethods에 추가하지 않았는데**

**도 잘 동작하는 경우가 있다!?**

**이 현상은 Spring의 CORS 처리 방식과 요청의 특성에 따라 달라질 수 있다.**

**왜 OPTIONS를 명시하지 않아도 동작할까?**  

-   **Spring의 기본 CORS 처리**  
    **Spring의 CorsConfiguration에서 setAllowedMethods에 특정 메서드만 명시하면, 그 메서드만 허용하는 것이 맞습니다.** **하지만, Spring Boot 또는 Spring Security가 내장된 기본 CORS 처리에서는 프리플라이트 요청(OPTIONS)을 자동으로 허용하는 경우가 많다.**

**즉, POST만 명시해도, 내부적으로 OPTIONS는 자동 허용되거나, 프레임워크가 OPTIONS 요청을 처리해 주**

**는 경우가 있다.**

**프리플라이트 요청은 다음과 같은 경우에만 발생**

-   커스텀 헤더 사용(예: Authorization, Content-Disposition 등) Content-Type이 application/json, multipart/form-data 등  
    단순하지 않은 타입일 때
-   PUT, DELETE, PATCH 등 단순하지 않은 HTTP 메서드 사용 시  
    만약 브라우저가 "단순 요청(Simple Request)"으로 판단하면, 프리플라이트 없이 바로 본 요청을 보낸다.

**프레임워크 버전 및 설정 영향**  

-   **Spring Boot 2.x 이상에서는 CORS 관련 자동 설정이 강화되어, 일부 경우 OPTIONS를 명시하지 않아도 정상 동작할 수 있다.**

**하지만, 보안이나 확장성, 유지보수 측면에서 명확하게 OPTIONS를 포함시키는 것이 좋다.**

* * *

**CORS와 CSRF의 관계**

-   공통점  
    둘 다 브라우저와 서버 간의 보안과 관련이 있으며, Spring Security 등에서 함께 설정하는 경우가 많음.
-   차이점  
    CORS는 "다른 출처에서의 접근 허용/제한"을 다루는 기술적 정책.  
    CSRF는 "인증된 사용자의 권한을 악용한 요청 위조"라는 공격 기법.
-   연결점  
    CORS는 교차 출처 요청을 제한해 악의적인 외부 사이트에서의 요청을 막을 수 있지만, CORS만으로 CSRF 공격을 완전히 차단할 수는 없음.
-   CSRF는 같은 출처에서 발생할 수 있기 때문.  
    즉, CORS는 교차 출처 요청만 제한하고, 같은 출처에서의 요청은 허용하므로 CSRF 공격은 여전히 가능하다.

**CSRF 방어를 위해서는 CSRF 토큰, SameSite 쿠키, Referer/Origin 검사 등 별도의 대책이 필요**
