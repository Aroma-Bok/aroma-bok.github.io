---
title: "[Spring] Spring Boot 입문(1)- 프로젝트 설정, Maven, Gradle"
date: 2021-11-19 23:33:52 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["spring", "springboot", "springboot 설정", "springboot 프로젝트", "스프링", "스프링부트", "스프링부트 설치", "스프링부트 시작"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-SpringBoot-%EC%8B%9C%EC%9E%91-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EC%84%A4%EC%A0%95-Maven-Gradle"
---
부족한 Spring 지식을 쌓아가기 위해서 '인프런'에서 무료 제공하는 강의를 보면서 공부하기 시작했다.

Maven, Gradle는 들어만 봤지 제대로 개념을 잡아보지는 못했다. 그래서 이렇게 강의를 들으면서 모르는 단어들이 나오면 찾아보면서 진행하려고 한다. 

* * *

### Maven : Spring 프로젝트에서 pom.xml이란 이름을 사용

### Gradle : 스프링부트, 안드로이드에서 사용

간단하게 정의를 해보면, **필요한 라이브러리를 땡겨오고 라이프사이클까지 관리해주는 Tool이다.**

요즘엔 Gradle을 많이 사용한다고 한다.

스프링 부트는 스타터 페이지를 통해서 필요한 부분들을 설정해서 만들 수 있다고 한다. (신기했다...)

![](/assets/img/posts/17/1.png)

*https://start.spring.io/*

이렇게 설정하고 다운로드한 파일을 풀어서 이클립스로 실행하면 아래와 같이 파일들이 생성된다.

![](/assets/img/posts/17/2.png)

src밑에 크게 main과 test 폴더가 있다. 그만큼 테스트 코드가 중요하다고 한다. (아직 와닿지 않는 주니어 개발자..)

Java파일 제외하고 나머지는 resources폴더에 있다.

옛날엔 스프링이 굉장히 까다롭고 설정이 어려웠다고 한다. 하지만 스프링 부트가 나오면서 개발자에게 친화적이 되었다고 한다.

그리고 **build.gradle** 파일을 열어보면 아래와 같은 코드가 있을 것이다.

```java
repositories {
	mavenCentral()
}
```

dependencies에서 정의해 놓은 것들을 메이븐 센트럴에서 다운로드하여라 라고 설정해놓은 코드라고 한다. 

지금은 gradle이 버전 설정하고 라이브러리 당겨오는구나 라고 생각하고 넘어가라고 해서 강사님의 말을 잘 듣고 넘어갔다..

그리고 main폴더 안에 있는 파일을 실행하면 아래와 같이 실행된다. 여기서 스프링 부트의 신기한 점은 톰캣이 자동으로 내장되어 있다는 점이다. 스프링 프레임워크랑은 달리 내장해서 사용하고 있다.

![](/assets/img/posts/17/3.png)

![](/assets/img/posts/17/4.png)

*스프링 부트 안에 내장되어 있는 톰캣*

P.S : 요즘엔 IntelliJ를 많이 사용한다고 한다. 그래서 필자도 사용해보고 싶지만, 회사에서 사용하는 이클립스에 좀 더 익숙해지고자 이클립스로 진행하였다. 꼭 다음엔 IntelliJ를 사용해보고 싶다.

출처 - 인프런 스프링 입문
