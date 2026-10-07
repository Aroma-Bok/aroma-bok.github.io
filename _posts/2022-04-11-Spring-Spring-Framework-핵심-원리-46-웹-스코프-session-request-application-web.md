---
title: "[Spring] Spring Framework - 핵심 원리 (46) - 웹 스코프 + session, request, application, websocket"
date: 2022-04-11 23:01:18 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["application", "request", "session", "websocket", "웹 스코프"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-46-%EC%9B%B9-%EC%8A%A4%EC%BD%94%ED%94%84-session-request-application-websocket"
---
### **웹 스코프 + session, request, application, websocket** 

* * *

**지금까지 싱글톤과 프로토타입 스코프를 학습했다. 싱글톤은 스프링 컨테이너의 시작과 끝까지 함께하는 매우 긴 스코프이고, 프로토타입은 생성과 의존관계 주입, 그리고 초기화까지만 진행하는 특별한 스코프이다.**

**이번에는 웹 스코프에 대해서 알아보자.**

#### **웹 스코프의 특징**

-   **웹 스코프는 웹 환경에서만 동작한다.**
-   **웹 스코프는 프로토타입과 다르게 스프링이 해당 스코프의 종료 시점까지 관리한다. 따라서 종료 메서드가 호출된다.**

#### **웹 스코프의 종류**

1.  **request : HTTP 요청 하나가 들어오고 나갈 때까지 유지되는 스코프, 각각의 HTTP 요청마다 별도의 빈 인스턴스가 생성되고, 관리된다.**
2.  **session : HTTP Session과 동일한 생명주기를 가지는 스코프.**
3.  **application : 서블릿 컨텍스트(ServletContext)와 동일한 생명주기를 가지는 스코프.**
4.  **websocket :  웹 소켓과 동일한 생명주기를 가지는 스코프.**

**사실 세션이나, 서블릿 컨텍스트, 웹 소켓 같은 용어를 잘 모르는 분들도 있을 것이다. 여기서는 request 스코프를 예제로 설명하겠다. 나머지도 범위만 다르지 동작 방식은 비슷하다.**

![](/assets/img/posts/123/1.png)

**A, B 동시에 호출한다고 해도, 각각 다른 객체 인스턴스를 할당한다.**
