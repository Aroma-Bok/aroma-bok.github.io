---
title: "[Spring] Spring Boot - 입문(8) - 비즈니스 요구사항 정리 (회원관리 예제)"
date: 2021-11-30 22:55:36 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["스프링", "스프링 부트", "스프링 부트 기본", "회원관리 예제"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Boot-%EC%9E%85%EB%AC%B88-%EB%B9%84%EC%A6%88%EB%8B%88%EC%8A%A4-%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%AD-%EC%A0%95%EB%A6%AC-%ED%9A%8C%EC%9B%90%EA%B4%80%EB%A6%AC-%EC%98%88%EC%A0%9C"
---
비즈니스 요구사항 정리

-   데이터 : 회원ID , 이름
-   기능 : 회원 등록, 조회
-   아직 데이터 저장소가 선정되지 않음 (가상의 시나리오)

![](/assets/img/posts/24/1.png)

컨트롤러 : 웹 MVC의 컨트롤러 역할

서비스 : 도메인 객체를 갖고 핵심 비즈니스 로직 구현 (ex : 회원ID 중복가입 방지)

리포지토리 : 데이터베이스에 접근, 도메인 객체를 DB에 저장하고 관리

도메인 : 비즈니스 도메인 객체 (ex : 회원, 주문, 쿠폰 등등 주로 DB에 저장하고 관리됨)

![](/assets/img/posts/24/2.png)

-   아직 데이터 저장소가 선정되지 않아서, 우선 인터페이스로 구현 클래스를 변경할 수 있도록 설계
-   데이터 저장소는 RDB, NoSQL등등 다양한 저장소를 고민중인 상황으로 가정
-   개발을 진행하기 위해서 초기 개발 단계에서는 구현체로 가벼운 메모리 기반의 데이터 저장소 사용

구체적인 코드는 다음편에!
