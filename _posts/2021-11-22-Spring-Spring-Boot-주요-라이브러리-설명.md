---
title: "[Spring] Spring Boot 입문(2)- 주요 라이브러리 설명"
date: 2021-11-22 23:37:44 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["spring", "springboot", "스프링", "스프링 라이브러리", "스프링기본", "스프링부트"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Boot-%EC%A3%BC%EC%9A%94-%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC-%EC%84%A4%EB%AA%85"
---
스프링부트의 핵심 라이브러리에 대해서 정리해 보았다.

#### **스프링 부트 라이브러리 : spring-boot-starter-web** 

-   spring-boot-starter-toncat : 톰캣(웹서버)
-   spring-webmvc : 스프링 웹 MVC
-   spring-boot-starter-thymeleaf : 타임리프 템플릿 엔진(View)
-   spring-boot-starter(공통) : 스프링 부트 + 스프링 코어 + 로깅  
    spring-boot -> spring-core 포함  
    spring-boot-stater-logging -> logback(log4j), slf4j 포함 - (표준에 가깝다고 한다)

\* 옛날엔 웹서버(WAS)걸 직접 서버에 설치하고 자바 코드를 밀어 넣었던 방식으로서 굉장히 힘든 작업.  
\* 임베디드 = 소스라이브러리에서 웹서버를 들고있다 

\*개발에서는 log는 System.out.println을 찍지 않고, logging을 사용해서 찍는다고 한다.  
Why? 필요한것만 얻어내기 위해서, 성능적으로도 좋다고 한다.

#### **테스트 라이브러리 : spring-boot-starter-test**

-   junit : 테스트 프레임워크
-   mockito : 목 라이브러리
-   assertj : 테스트 코드를 좀 더 편하게 작성하게 도와주는 라이브러리
-   spring-test : 스프링 통합 테스트 지원

출처 : 인프런 스프링 입문강의
