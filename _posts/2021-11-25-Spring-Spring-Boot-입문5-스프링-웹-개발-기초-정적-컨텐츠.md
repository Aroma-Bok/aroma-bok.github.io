---
title: "[Spring] Spring Boot - 입문(5) - 스프링 웹 개발 기초 [정적 컨텐츠]"
date: 2021-11-25 23:36:02 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["스프링", "스프링기본", "스프링부트", "스프링부트기본", "정적페이지"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Boot-%EC%9E%85%EB%AC%B85-%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9B%B9-%EA%B0%9C%EB%B0%9C-%EA%B8%B0%EC%B4%88-%EC%A0%95%EC%A0%81-%EC%BB%A8%ED%85%90%EC%B8%A0"
---
정적 페이지 - **파일을 그냥 웹으로 내려주는 방식**

MVC와 템플릿 엔진 - 뭔가 **HTML을 서버에서 프로그래밍을 해서 동적으로 내려주는 방식**  
(요즘 사용하는 것)

API - 만약 안드로이드, IOS를 같이 개발해야한다면 **JSON을 사용해서 클라이언트에게 전달하는 방식**

#### **정적페이지 생성**

1\. resources -> static 폴더안에 html파일을 하나 만들어서 URL에 검색하면, 정적페이지가 생성된다.

![](/assets/img/posts/21/1.png)

![](/assets/img/posts/21/2.png)

#### **설명**

![](/assets/img/posts/21/3.png)

1.  **내장 톰켓 서버가 요청을 받고**
2.  **컨트롤러에서 우선적으로 hello-ststic 컨트롤러가 있는지 확인**
3.  **없으면, resources->static 안에서 확인**
