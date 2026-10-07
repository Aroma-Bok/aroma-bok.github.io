---
title: "web.xml, dispatcher-servlet.xml, pom.xml 개념과 차이점 그리고Spring 웹 애플리케이션 동작순서"
date: 2023-08-25 14:07:01 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["dispatcher-servlet.xml", "dispatcher-servlet.xml vs web.xml vs pom.xml 개념과 차이", "pom.xml", "spring 웹 애플리케이션 동작 순서", "web.xml"]
tistory_url: "https://aroma-bok.tistory.com/entry/webxml-dispatcher-servletxml-pomxml-%EA%B0%9C%EB%85%90%EA%B3%BC-%EC%B0%A8%EC%9D%B4%EC%A0%90-%EA%B7%B8%EB%A6%AC%EA%B3%A0Spring-%EC%9B%B9-%EC%95%A0%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98-%EB%8F%99%EC%9E%91%EC%88%9C%EC%84%9C"
---
**dispatcher-servlet.xml, web.xml 그리고 pom.xml은 각각 Spring 웹 애플리케이션의 구성파일이다.**

**이 파일들은 Spring MVC 프레임워크를 기반으로 한 웹 애플리케이션의 설정, 배포, 의존성, 관리 등을 담당한다.**

**각 파익의 역할과 동작하는 순서를 간단하게 알아보자.**

* * *

#### **web.xml**

-   **서블릿 기반 웹 애플리케이션의 배포 서술자(Deployment Descriptor)로, 웹 애플리케이션의 구성 및 설정 정보를  
    ****담고 있다.**
-   **웹 애플리케이션 시작 시 컨테이너가 web.xml을 읽어서 설정을 초기화하고 필요한 서블릿 및 필터를 등록한다.**
-   **Spring MVC의 DispatcherServlet 역시 web.xml에서 설정하며, 애플리케이션의 모든 요청을 받아들이고 처리하는 역할을 수행한다.**

* * *

#### **dispatcher-servlet.xml**

-   **Spring MVC 웹 애플리케이션의 설정 파일로, Spring MVC 관련된 빈(Bean) 설정을 포함한다.**
-   **컨트롤러, 뷰, 리졸버, 핸들러 매핑 등 Spring MVC에서 사용되는 컴포넌트들을 정의하고 구성**
-   **web.xml에 설정된 DispatcherServlet의 설정파일 경로로 지정된다.**

```sql
// web.xml에서 설정된 내용
	<servlet>
        <servlet-name>action</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/config/springmvc/dispatcher-servlet.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
```

* * *

#### **pom.xml**

-   **Maven을 사용하는 프로젝트의 설정파일로, 프로젝트의 의존성 관리와 빌드 설정을 정의**
-   **프로젝트에서 필요한 라이브러리 및 의존성을 선언하고, Maven의 빌드 라이프사이클을 이용하여 애플리케이션을  
    빌드하고 배포할 수 있다.**

> **pom.xml은 빌드와 의존성 관리에 사용되므로, 웹 애플리케이션의 동작과 직접적인 연관은 없다.**  
> **하지만 Maven을 사용하여 프로젝트를 관리하며 필요한 라이브러리와 빌드 설정을 쉽게 관리할 수 있다.**  
>   
> **pom.xml에 설정된 빌드 라이프사이클과 명령어에 따라 컴파일, 테스트, 패키징 등의 작업 수행**

* * *

#### **Spring 웹 애플리케이션 동작순서**

1.  **웹 애플리케이션을 시작하면서 서블릿 컨테이너는 web.xml을 로드하고 애플리케이션 컨텍스트를 설정**
2.  **web.xml에 정의된 DispatcherServlet이 초기화**
3.  **DispatcherServlet은 dispatcher-servlet.xml을 로드하며, Spring MVC의 관련 설정이 초기화**
4.  **클라이언트로부터 요청이 들어오면 DispatcherServlet이 해당 요청을 처리할 적절한 컨트롤러에 전달**
5.  **컨트롤러가 요청을 처리하고 필요한 비즈니스 로직 수행**
6.  **컨트롤러는 모델 데이터와 뷰 이름을 반환하여 DispatcherServlet에게 리턴**
7.  **DispatcherServlet은 뷰 리졸버를 사용하여 뷰를 찾고 렌더링**
8.  **렌더링 결과는 클라이언트에게 반환되어 표시**
