---
title: "[Spring Boot] Spring Boot 3.x 실행 오류 || No matching variant of org.springframework.boot:spring-boot-gradle-plugin"
date: 2023-03-06 21:17:59 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["no matching variant of org.springframework.boot:spring-boot-gradle-plugin", "spring boot 2 오류", "spring boot 3"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Boot-Spring-Boot-3x-%EC%8B%A4%ED%96%89-%EC%98%A4%EB%A5%98-No-matching-variant-of-orgspringframeworkbootspring-boot-gradle-plugin"
---
**이클립스만 쓰다가 인텔리 J로 갈아타기 위해서 혼자 프로젝트를 진행하려고 설정을 세팅하는 과정에서 생겨난 해프닝이다.** 

#### **문제**

**Gradle import 하는 과정에서 No matching variant of org.springframework.boot:spring-boot-gradle-plugin 에러 발생**

![](/assets/img/posts/188/1.png)

* * *

#### **해결방법**

**22년 11월 Spring Boot 3가 정식 릴리즈 되면서 Java 17 이상만 지원하기로 변경되었다.** 

**필자는 Java 11 버전으로 사용하기 위해서 Spring Boot를 2.x버전으로 다운그레이도 하여 사용하였다.**

**그 외 이 같은 오류가 발생한다면 아래 방법들을 생각해 두고 처리해 보자.**

**1\. Spring Boot 2.x 버전으로 변경**

**2\. Java 17 이상으로 변경 (Spring Boot 3.x 버전 이상 사용할 때)**

**(Gradle JVM , Project SDK에 설정된 JDK 버전도 변경해주어야 하는 것도 잊지 말자)**

![](/assets/img/posts/188/2.png)

*해결*
