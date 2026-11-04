---
title: "API Gateway는 왜 WebFlux를 쓸까 - MVC와의 차이, 블로킹·논블로킹 정리"
date: 2026-11-04 09:00:00 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["spring-cloud-gateway", "webflux", "spring-mvc", "reactive", "api-gateway", "msa", "netty", "tomcat", "spring-security", "mono"]
tistory_url: "https://aroma-bok.tistory.com/entry/API-Gateway%EB%8A%94-%EC%99%9C-WebFlux%EB%A5%BC-%EC%93%B8%EA%B9%8C-MVC%EC%99%80%EC%9D%98-%EC%B0%A8%EC%9D%B4-%EB%B8%94%EB%A1%9C%ED%82%B9%C2%B7%EB%85%BC%EB%B8%94%EB%A1%9C%ED%82%B9-%EC%A0%95%EB%A6%AC"
---
### 들어가며

전자정부 표준프레임워크의 MSA 교육용 템플릿(`egovframe-msa-edu`)을 로컬에서 직접 띄워 보다가 이상한 점을 하나 발견했다. 같은 프로젝트 안인데 **user-service는 Spring MVC로, apigateway는 WebFlux로** 짜여 있었다. 평소 Spring Security를 MVC 방식으로만 써 왔기 때문에, 게이트웨이 코드의 `ServerHttpSecurity`, `pathMatchers`, `Mono` 같은 이름이 낯설었다.

그리고 이런 의문이 들었다. **"어차피 게이트웨이 뒤의 서비스는 MVC로 처리하는데, 게이트웨이만 WebFlux로 갈 이유가 있나?"** 이 글은 그 의문을 풀면서 정리한 내용이다.

### 1. 먼저 결론

- **WebFlux는 기능이 다른 것이 아니라 요청을 처리하는 스레드 사용 방식이 다르다.** 기다리는 동안 스레드를 놓아 준다.
- **게이트웨이는 일은 거의 하지 않고 뒤 서버의 응답을 기다리기만 하는 곳**이라서, 이 방식과 잘 맞는다.
- 뒤쪽 서비스가 MVC인지 WebFlux인지는 게이트웨이의 선택과 **상관이 없다.** 둘은 서로 독립이다.
- 다만 **트래픽이 적은 환경에서는 차이가 거의 체감되지 않는다.**

### 2. MVC와 WebFlux, 무엇이 다른가

| | Spring MVC | Spring WebFlux |
|---|---|---|
| 처리 방식 | 요청 하나에 스레드 하나가 끝까지 처리 (동기·블로킹) | 적은 스레드가 이벤트 단위로 나눠 처리 (비동기·논블로킹) |
| 기본 서버 | Tomcat | Netty |
| 반환 타입 | 값 그대로 (`Boolean`, `User`) | `Mono<T>`, `Flux<T>` |
| 코드 난이도 | 익숙하고 쉬움 | 어렵고 디버깅이 까다로움 |

**`Mono`는 "나중에 도착할 값 하나를 담는 상자"** 라고 이해하면 쉽다. 값이 아직 없을 수 있으니 `.map()`, `.timeout()` 같은 함수로 "값이 오면 이렇게 처리해라"를 미리 이어 붙인다.

### 3. 식당 비유로 보는 스레드 모델

- **MVC**: 손님 한 명에 직원 한 명이 붙어서 주문부터 서빙까지 **끝까지 담당**한다. 요리가 나올 때까지 그 자리에서 기다린다.
- **WebFlux**: 직원 몇 명이 주문만 받고 **다른 손님에게 간다.** 요리가 나오면 그때 비어 있는 직원이 서빙한다.

손님이 몰리면 MVC는 직원(스레드)이 모자라서 줄이 길어지고, WebFlux는 적은 직원으로도 버틴다. 단, 요리 하나를 **더 빨리 만들어 주는 것은 아니다.** 많은 손님을 동시에 받는 능력이 다를 뿐이다.

### 4. 게이트웨이는 "기다리기만 하는 곳"

요청 하나가 거치는 경로를 그려 보면 이렇다.

```
브라우저 → 게이트웨이 → 백엔드(MVC) → DB
          └──────── 게이트웨이는 이 전체를 기다린다 ────────┘
```

- **백엔드**는 DB 조회나 계산 같은 **실제 일**을 하는 동안 스레드를 쓴다. 점유해도 낭비가 적다.
- **게이트웨이**는 요청을 전달만 하고 **백엔드가 끝날 때까지 기다린다.** 이 기다림 시간에 백엔드의 처리 시간이 통째로 들어간다.

### 5. 게이트웨이가 MVC(블로킹)라면

- **스레드 1개가 백엔드 응답이 올 때까지 놀면서 묶인다.** 요청이 100건이면 100개가 묶인다.
- 게이트웨이의 **스레드 수가 시스템 전체의 동시 처리 한도**가 된다. Tomcat 기본 스레드가 200개 정도라서, 한도는 그 근처다.
- **느린 서비스 하나가 전체를 막을 수 있다.** 한 서비스 응답이 느려지면 게이트웨이 스레드가 거기에 붙들려, 멀쩡한 다른 서비스로 가는 요청까지 처리하지 못한다.

WebFlux 게이트웨이는 백엔드를 기다리는 동안 스레드를 점유하지 않으므로 이런 상황에 상대적으로 강하다.

### 6. 헷갈리기 쉬운 오해 세 가지

- **"뒤 서버가 MVC면 게이트웨이도 MVC여야 하지 않나?"**: 아니다. 둘은 독립이다. 게이트웨이 방식은 뒤 서버 방식과 무관하다.
- **"응답이 다른 서버에서 오는 건가?"**: 아니다. 응답은 요청을 보낸 **같은 백엔드 서버**에서 온다. 비동기의 핵심은 응답이 올 때까지 **스레드를 붙들지 않는 것**이고, 응답이 도착하면 그때 비어 있는 스레드가 이어서 처리한다.
- **"WebFlux가 요청 하나를 더 빠르게 하나?"**: 아니다. 이득은 **동시 요청이 많을 때 적은 자원으로 버티는 것**이다.

그리고 이 이점은 MSA에만 해당하는 것이 아니라, 받아서 넘기고 기다리는 **모든 중계 서버**에 해당한다.

### 7. 코드로 보기: 같은 Spring Security, 다른 클래스

Spring Security는 같은 라이브러리인데, 웹 방식에 따라 **쓰는 클래스가 갈라진다.** 템플릿의 user-service(MVC)와 apigateway(WebFlux)에서 실제로 본 것을 대응시키면 이렇다.

| 역할 | MVC 방식 | WebFlux 방식 |
|---|---|---|
| 설정 객체 | `HttpSecurity` | `ServerHttpSecurity` |
| 필터 체인 | `SecurityFilterChain` | `SecurityWebFilterChain` |
| 활성화 어노테이션 | `@EnableWebSecurity` | `@EnableWebFluxSecurity` |
| 경로 매칭 | `requestMatchers(...)` | `pathMatchers(...)` |
| 권한 판단 | `AuthorizationManager` | `ReactiveAuthorizationManager` |
| 요청 객체 | `HttpServletRequest` | `ServerHttpRequest` |
| 판단 결과 | `AuthorizationDecision` | `Mono<AuthorizationDecision>` |

게이트웨이의 권한 확인 코드는 뒤 서버(user-service)에 "이 사용자가 이 경로에 접근 가능한가"를 묻고 응답을 받아 결정한다. 핵심 부분만 줄이면 이런 모양이다.

```java
return webClientBuilder.clone()
    .baseUrl(baseUrl)
    .build()
    .get()
    .retrieve()
    .bodyToMono(Boolean.class)   // 응답이 오면 Boolean으로
    .timeout(Duration.ofSeconds(30))
    .defaultIfEmpty(false)
    .map(AuthorizationDecision::new);
```

값을 바로 돌려주는 것이 아니라 **"응답이 오면 이렇게 처리해라"를 이어 붙인 것**을 반환한다. 기다리는 동안 스레드를 놓기 위한 형태다.

### 8. 그래서 업무 서비스도 WebFlux로 가야 할까

그럴 필요는 없다.

- **게이트웨이**: 기다리기만 하고 모든 요청이 지나가는 곳이라 WebFlux가 잘 맞는다. 게다가 Spring Cloud Gateway는 WebFlux 위에 만들어진 도구다.
- **업무 서비스**: DB를 쓰는 일반 업무는 코드가 단순한 MVC가 낫다.

그래서 한 프로젝트 안에서 게이트웨이만 WebFlux, 나머지는 MVC로 섞는 구성이 흔하다. 트래픽이 많지 않은 환경이라면 어느 쪽이든 체감 차이는 크지 않을 것이다. 이 글의 성능 이야기는 일반 개념을 정리한 것이고 직접 측정한 수치는 아니다.

### 마치며

한 줄로 줄이면 이렇다.

**게이트웨이는 일은 안 하고 기다리기만 하면서 모든 요청이 지나가는 곳이라서, 기다리는 동안 스레드를 놓아 주는 WebFlux가 맞다.**

다음 글에서는 같은 템플릿에서 만난 **Eureka(서비스 디스커버리)** 와, 포트를 고정해서 쓰는 방식과의 차이를 정리해 볼 생각이다.

### 참고

- 표준프레임워크 MSA 교육용 템플릿 (egovframe-msa-edu)
- Spring WebFlux, Spring Cloud Gateway 공식 문서
