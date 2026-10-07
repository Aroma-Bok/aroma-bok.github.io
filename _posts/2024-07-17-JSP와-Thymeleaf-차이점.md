---
title: "JSP와 Thymeleaf 차이점"
date: 2024-07-17 08:21:43 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["jsp", "jsp thymeleaf 차이점", "jsp vs thymeleaf", "thymeleaf"]
tistory_url: "https://aroma-bok.tistory.com/entry/JSP%EC%99%80-Thymeleaf-%EC%B0%A8%EC%9D%B4%EC%A0%90"
---
### **JSP vs Thymeleaf**

**JSP는 일반적으로 Java MVC 구조에서 View의 역할을 하는데, 요즘은 Spring에서는 View의 역할로**

**Thymeleaf를 더 선호한다.** 

**JSP와 Thymeleaf의 가장 큰 차이는 Jar파일로 export 가능한지의 여부이다.**

**Spring으로 빌드하면 기본적으로 Jar파일로 빌드가 되는데, JSP는 Jar 패키징이 불가능하고,**

**War 패키징만 가능하다.** **그래서 Jar로 패키징이 가능한 Thymeleaf를 사용하는 것이 더 좋고 편리하다.**

**War로 패키징을 하려면 WAS가 필요하고 사전에 정의된 구조만을 사용해야 하기 때문에 복잡하다.**

**그래서 Jar로 패키징 하는 것이 더 편리하다.**

**Spring Boot + JSP로 프로젝트를 Jar로 빌드해 실행하면, 브라우저에 오류가 뜨는 것을 볼 수 있을 것이다.**

**내장 톰캣을 사용하는 Spring Boot Application에서 JSP를 지원하지 않아 발생하는 오류이다.**

* * *

### **JSP \[Java Server Page\]**

**\- HTML에 Java 코드를 사용하여 동적 웹페이지를 생성하는 웹 어플리케이션 도구**

**\- JSP가 실행되면 자바 서블릿(Servlet)으로 변환되며 웹 어플리케이션 서버에서 동작되면서 필요한 기능을 수행하고,** **생성된 데이터를 웹페이지와 함께 클라이언트로 응답한다.**

**\- 일반적으로 JSP 파일 내부에 Java 코드를 넣는 것은 추천하지 않는다.**

**\- JSP는 순수 HTML을 유지하지 않기 때문에, JSP 코드와 HTML 코드가 뒤죽박죽 섞여 웹 브라우저에서**

**정상적인 결과를 확인하기 힘들다.**

**\- JSP는 오직 서버를 통해서 렌더링 되고, HTML 응답 결과를 받아야만 화면을 확인할 수 있다.**

> **서블릿(Servlet) : 웹페이지를 동적으로 생성하기 위한 서버 측 프로그램이며, WAS 위에서 컴파일되고 동작한다**.

* * *

### **Thymeleaf**

**\- HTML 파일을 가져와 파싱하고 그것을 분석해 정해진 위치에 데이터를 뿌려준다.**

**\- Thymeleaf는 Servlet으로 변환되지 않아 비즈니스 로직이 완전히 분리된다.**

**\- 순수 HTML 파일을 최대한 유지한다. 그래서 웹 브라우저에서 파일을 직접 열어도 내용을 확인할 수 있다.** 

**\- JSP보다 응답 속도가 느리다는 단점이 있다.**

* * *

### **정리**

-   **JSP는 War 패키징만 가능하며, Jar 패키징이 불가능하다.**
-   **JSP는 오직 서버를 통해서만 확인이 가능하다.**
-   **Thymeleaf는 Jar패키징이 가능하다.**
-   **Thymeleaf는 HTML 파일을 최대한 유지하기 때문에, 웹 브라우저에서 직접 열어서 내용을 확인가능 하다.**
