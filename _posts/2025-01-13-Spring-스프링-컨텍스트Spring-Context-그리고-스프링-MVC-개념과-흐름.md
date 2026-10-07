---
title: "[Spring] 스프링 컨텍스트(Spring Context) 그리고 스프링 MVC 개념과 흐름"
date: 2025-01-13 17:31:19 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["spring context", "스프링 mvc", "스프링 mvc 흐름", "스프링 컨텍스트", "스프링 컨텍스트 흐름", "컨텍스트"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-%EC%8A%A4%ED%94%84%EB%A7%81-%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8Spring-Context-%EA%B7%B8%EB%A6%AC%EA%B3%A0-%EC%8A%A4%ED%94%84%EB%A7%81-MVC-%EA%B0%9C%EB%85%90%EA%B3%BC-%ED%9D%90%EB%A6%84"
---
#### **스프링 컨텍스트 (Spring Context)**

-   **스프링 프레임워크의 핵심 기능으로, 애플리케이션에서 사용되는 객체(빈, Bean)를 생성, 관리, 설정, 조립하고 제공하는 중앙관리 시스템**
-   **애플리케이션 실행에 필요한 구성 요소를 관리하는 컨테이너(Container)**

![](/assets/img/posts/302/1.png)

#### **스프링 컨텍스트 역할**

**1. 객체 생성 및 관리**

**▶ @Component, @Service, @Repository, @Bean 등의 어노테이션으로 등록된 객체를 생성하고 관리**

**2\. 의존성 주입(Dependency Injection, DI)**

****▶** 객체 간의 의존 관계를 자동으로 연결해 주는 역할**

****▶** 이를 통해, 코드에서 객체 생성 및 초기화 과정을 단순화**

**3. 빈(Bean) 검색 및 제공**

****▶** 필요한 객체를 컨텍스트에서 검색해서 제공하며, 이때 개발자가 직접 객체를 생성하지 않아도 된다**

**4\. 라이프사이클 관리**

****▶** 스프링 컨텍스트는 빈의 생명주기(LifeCycle)를 관리**

**5. AOP(Aspect-Oriented Programming) 지원**

****▶** 스프링 컨텍스트는 부가적인 동작(예: 로그, 트랜잭션 관리)을 객체에 쉽게 적용할 수 있도록 AOP 기능 제공**

**6. 이벤트 처리**

****▶** 애플리케이션 내에서 발생하는 이벤트를 발생하고 이를 수신하는 기능도 제공**

#### **스프링 컨텍스트의 주요 구현체**

**1. ApplicationContext**

****▶** 스프링 컨텍스트의 대표적인 인터페이스**

****▶** 주요 구현체**

-   **ClassPathXmlApplicationContext : XML 파일 기반 설정**
-   **AnnotationConfigApplicationContext : 자바 클래스 기반 설정**
-   **WebApplicationContext : 웹 애플리케이션 전용 컨텍스트**

**2. BeanFactory**

****▶** ApplicationContext의 상위 개념으로, 간단한 DI 기능만 제공**

****▶** 현재는 ApplicationContext가 사용됨**  
  

#### **스프링 컨텍스트의 구성 요소**

**1\. 빈(Bean)**

******▶**** 컨테스트에 등록된 객체**

******▶**** 주로 @Component, @Service, @Repository, @Controller 또는 @Bean으로 정의**  
**2. 설정 메타정보**

******▶**** 애플리케이션의 설정 정보(XML, Java Config, Annotation)**  
**3. 빈 정의(Bean Definition)**

******▶**** 스프링 컨텍스트는 설정 정보를 기반으로 빈의 생성 방식, 스코프, 의존성 등을 정의**

* * *

#### **Spring MVC**

-   **Model-View-Controller 패턴을 기반으로 웹 애플리케이션을 개발하기 위한 강력한 프레임워크**

![](/assets/img/posts/302/2.png)

#### **Spring MVC의 주요 구성 요소**

**1. DispatcherServlet**

******▶**** Spring MVC의 중심 컨트롤러 역할을 수행**

******▶**** 모든 요청을 받아서 적절한 핸들러로 요청을 위임하고, 응답을 생성하는 과정을 관리**

**2\. HandlerMapping**

******▶**** URL과 컨트롤러를 매핑**

******▶**** 요청 URL에 따라 어떤 컨트롤러 메서드를 실행할지 결정**

**3\. Controller**

******▶**** 비즈니스 로직을 처리하고, 모델 데이터를 준비하거나 요청을 처리한 결과 반환**

**4\. ModelAndView**

******▶**** 컨트롤러에서 반환되는 객체로, 데이터(Model)와 뷰(View)를 함께 포함**

**5\. ViewResolver**

******▶**** 어떤 뷰를 렌더링 할지 결정**

******▶**** JSP, Thymeleaf, FreeMaker 등의 뷰 템플릿 엔진과 연동**

**6\. View**

******▶**** 최종적으로 사용자에게 보이는 화면**

******▶**** ViewResolver가 결정한 뷰 템플릿 엔진에 의해 렌더링**

#### **  
Spring MVC 요청 처리 흐름**

**1. 클라이언트 요청**

******▶**** 사용자가 브라우저에서 URL로 요청**

******▶**** 요청은 DispatcherServlet으로 전달**

**2\. DispatcherServlet이 요청 처리 시작**

******▶**** Spring 컨텍스트(ApplicationContext)에서 필요한 빈을 로드**

******▶**** 적절한 HandlerMapping을 통해 요청을 처리할 컨트롤러 찾기**

**3\. HandlerAdapter 호출**

******▶**** HandlerAdapter는 선택된 컨트롤러의 메서드를 실행할 수 있도록 도와줌**

******▶**** 컨트롤러 메서드가 실행되면, 결과를 ModelAndView 객체로 반환**

**4\. ModelAndView 생성**

******▶**** 컨트롤러는 처리 결과 데이터를 Model에 담고, 어떤 뷰를 렌더링 할지 뷰이름(ViewName)을 반환**

**5\. ViewResolver가 뷰를 결정**

******▶**** 반환된 ViewName을 바탕으로 적절한 뷰 템플릿을 선택**

******▶**** 예: InternalResourceViewResolver는 JSP 파일을 찾는다**

**6\. View 렌더링**

******▶**** 선택된 뷰(View)가 Model 데이터를 사용해 최종 HTML 응답을 생성**

**7\. DispatcherServlet이 응답 반환**

******▶**** 최종적으로 생성된 응답을 클라이언트에게 반환**
