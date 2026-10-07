---
title: "[Server] WAS(Web Application Server) vs Web Server 그리고 Web Container란? 개념 정리"
date: 2024-12-24 10:36:51 +0900
categories: ["Solution & Tools", "Server"]
tags: ["java 어플리케이션 서버", "was web container web server", "was web server 차이", "was 구조", "was 웹 컨테이너 포함", "was란", "web application server", "web container", "web container was 차이", "웹 컨테이너란"]
tistory_url: "https://aroma-bok.tistory.com/entry/Server-WASWeb-Application-Server-vs-Web-Server-%EA%B7%B8%EB%A6%AC%EA%B3%A0-Web-Container%EB%9E%80-%EA%B0%9C%EB%85%90-%EC%A0%95%EB%A6%AC"
---
### **Application Server vs Web Server**

#### **  
Web Application Server**

-   **Java EE 애플리케이션 실행 환경 제공**
-   **동적 비즈니스 로직 처리 (ex: EJB, JPA)**
-   **트랜잭션 관리, 메시징 서비스, 보안 등 지원**
-   **대표적인 WAS  
    Apache Tomcat, JBoss/WildFly, WebLogic, WebSphere, GlassFish, Jetty 등**

#### **  
Web Server**

-   **정적 콘텐츠 (HTML, CSS, JavaScript) 제공**
-   **서블릿, JSP 같은 동적 컨텐츠는 처리하지 않음**
-   **대표적인 Web Server**  
    **Apache HTTP Server, Nginx, Microsoft IIS, Caddy 등**

![](/assets/img/posts/285/1.png)

* * *

### **웹 서버(Web Server) / 웹 컨테이너(Web Container) / WAS(Web Applicaiton Server)**

**▶ 3가지 모두 웹 애플리케이션의 요청과 응답을 처리하는데 관련된 서버 기술**

### **웹 서버(Web Server)**

-   **정적 컨텐츠(HTML, CSS, JavaScript, 이미지 등)를 클라이언트(브라우저)에 제공**
-   **HTTP 요청을 처리하고, 정적 파일을 반환하거나 동적 요청을 다른 서버 (예: WAS)로 전달**
-   **주로 리소스 관리와 로드 밸런싱 역할도 수행**

#### **특징**

-   **동적 컨텐츠를 직접 처리하지 않음. 동적 요청은 WAS로 전달**
-   **빠르고 가볍게 동작, 트래픽 분산(로드밸런싱)을 지원**

#### **흐름**

1.  **클라이언트 > 웹서버 요청 (HTTP GET 요청 : 정적페이지 요청)**
2.  **웹 서버가 요청을 처리하고, 정적 컨텐츠 반환**
3.  **동적 요청은 WAS로 전달**

### **웹 컨테이너(Web Container)**

-   **Java 기반 웹 애플리케이션에서 서블릿(Servlet)과 JSP(Java Server Page)를 실행하는 환경**
-   **HTTP 요청/응답 처리를 JAVA EE(Java EE / Jakarta EE) 사양에 따라 수행**
-   **WAS의 일부로 포함되어 동작하며, 서블릿 생명 주기 관리 등 Java 웹 애플리케이션의 핵심 역할 담당**

#### **특징**

-   **Java 기반 동적 웹 애플리케이션을 실행하기 위한 필수 환경**
-   **서블릿 및 JSP 실행**
-   **클라이언트와의 세션 관리, HTTP 요청 파싱 등 처리**

#### **흐름**

1.  **클라이언트 > 웹 서버 요청(HTTP POST 요청: Java 서블릿 실행 요청)**
2.  **웹 서버는 요청을 웹 컨테이너로 전달**
3.  **웹 컨테이너가 서블릿을 실행하고 동적 컨텐츠 반환**

### **WAS(Web Applicaiton Server)**

-   **동적 웹 애플리케이션을 실행하고, 비즈니스 로직을 처리하는 서버**
-   **웹 서버와 웹 컨테이너를 포함하며, 더 나아가 데이터베이스 연결, 트랜잭션 처리, 보안, 클러스터링 등의  
    엔터프라이즈 기능 제공**

#### **특징**

-   **웹 서버와 달리 동적 컨텐츠(서블릿, JSP)를 처리**
-   **데이터베이스 연결, 트랜잭션 관리, 비즈니스 로직 처리 등의 엔터프라이즈 기능 제공**
-   **대규모 애플리케이션을 위한 확장성과 안정성 지원**

#### **흐름**

1.  **클라이언트 > 웹서버 > WAS 요청 전달**
2.  **WAS는 웹 컨테이너에서 서블릿/JSP 실행**
3.  **비즈니스 로직 처리, 데이터베이스 작업 수행 후 응답 생성**
4.  **클라이언트로 응답 반환**

![](/assets/img/posts/285/2.png)

* * *

### **WAS에 웹 서버(Web Server)와 웹 컨테이너(Web Container)가 포함되어 있는가?**

**WAS에 웹 서버가 포함될 수도 있고, 별도로 존재할 수도 있다.**

#### **포함되는 경우**

-   **WAS 자체에 웹 서버 기능을 내장한 경우, 정적 콘텐츠와 동적 콘텐츠를 모두 처리**
-   **예: Apache Tomcat은 웹 서버와 웹 컨테이너 기능을 모두 제공**

#### **분리되는 경우**

-   **웹 서버와 WAS를 분리하여 아키텍처를 구성**
-   **예: Nginx(웹 서버) + JBoss/WildFly(WAS)**

#### **  
분리 아키텍처의 이유**

#### **성능 최적화**

-   **웹 서버가 정적 콘텐츠(HTML, CSS, JS 등)를 빠르게 처리**
-   **WAS는 동적 콘텐츠(서블릿, JSP 등)만 처리하도록 부담 감소**

#### **보안 및 확장성**

-   **웹 서버가 방화벽 역할을 수행하여 WAS를 보호**
-   **여러 WAS 인스턴스에 트래픽을 분산(로드 밸런싱)**

#### **예시**

-   **Nginx(웹 서버): 정적 리소스 처리 및 WAS로 요청 전달 및 로드밸런싱**
-   **JBoss/WildFly(WAS): 동적 요청 처리**
