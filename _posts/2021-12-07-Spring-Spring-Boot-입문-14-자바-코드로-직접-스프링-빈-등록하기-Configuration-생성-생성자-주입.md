---
title: "[Spring] Spring Boot - 입문 (14) - 자바 코드로 직접 스프링 빈 등록하기 + Configuration 생성 + 생성자 주입 + 필드 주입 + Setter 주입"
date: 2021-12-07 23:03:59 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["di", "setter 주입", "spring bean", "생성자 주입", "스프링 부트", "스프링 부트 입문", "스프링 빈", "필드 주입"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Boot-%EC%9E%85%EB%AC%B8-14-%EC%9E%90%EB%B0%94-%EC%BD%94%EB%93%9C%EB%A1%9C-%EC%A7%81%EC%A0%91-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B9%88-%EB%93%B1%EB%A1%9D%ED%95%98%EA%B8%B0-Configuration-%EC%83%9D%EC%84%B1-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%A3%BC%EC%9E%85-%ED%95%84%EB%93%9C-%EC%A3%BC%EC%9E%85-Setter-%EC%A3%BC%EC%9E%85"
---
#### **자바 코드로 직접 스프링 빈 등록하기**

* * *

@Component 스캔을 사용해서 자동으로 빈을 등록하는 방법이 아닌, 직접 설정 파일에 등록하는 방법을 배워보자.

**스프링 빈 등록**하는 방법은 **2가지** 다 알아야 한다!!

**1) Component 자동 스캔 방법**

**2) Java코드로 직접 스프링 빈 등록**

먼저, Controller를 제외하고, Service와 Repository에서 어노테이션을 다 지워주고 돌려보자.

결과는 당연히 에러가 발생한다.

```java
Error starting ApplicationContext. To display the conditions report re-run your application with 'debug' enabled.
2021-12-07 22:17:21.494 ERROR 4580 --- [           main] o.s.b.d.LoggingFailureAnalysisReporter   : 

***************************
APPLICATION FAILED TO START
***************************

Description:

Parameter 0 of constructor in hello.hellospring.controller.MemberController required a bean of type 'hello.hellospring.service.MemberService' that could not be found.

Action:

Consider defining a bean of type 'hello.hellospring.service.MemberService' in your configuration.
```

그러면 어떻게 자바 코드로 Bean을 등록할 수 있을까?

#### **1\. Configuration 파일 생성**

![](/assets/img/posts/30/1.png)![](/assets/img/posts/30/2.png)

#### **2\. Bean 등록** 

![](/assets/img/posts/30/3.png)

**@Bean : 스프링 빈을 등록할거야! 라는 의미!**

**스프링이 뜰 때, Configuration을 읽고 Bean을 등록해준다.**

MemberService는 파라미터로 MemberRepository가 필요하다. (아래 참고)

![](/assets/img/posts/30/4.png)

*기존에 만들어둔 MemberService*

똑같이 MemberRepository를 Bean으로 등록해주고, MemberService에 넣어주자.

![](/assets/img/posts/30/5.png)

위 내용처럼 진행이 되면, 아래처럼 **관계가 형성**된다고 한다.

![](/assets/img/posts/30/6.png)

Controller는 스프링이 관리를 해야하는거라서 어노테이션을 지우지 않아야 한다.

Component스캔이기 때문에 자동으로 스프링이 Bean으로 등록해준다.

![](/assets/img/posts/30/7.png)

*Controller*

이렇게 Configuration에 Bean을 등록하고 실행시키면 당연히 잘 돌아간다.

![](/assets/img/posts/30/8.png)

편한 건 당연히 앞서 배웠던, Component스캔이 편하다. 하지만, 2가지 다 **꼭** 알아두라고 한다.

**스프링 빈 등록하는 방법**

1\. Component스캔

2\. 직접 Bean 등록

참고 :

1\. 옛날엔 XML 문서에 작성을 했다고 한다. 요즘 실무에서는 잘 사용하지 않는다고 한다.

2\. DI에는 필드주입, Setter주입, 생성자 주입 이렇게 3가지 존재

![](/assets/img/posts/30/9.png)

*생성자 주입*

![](/assets/img/posts/30/10.png)

*필드 주입*

\* 필드 주입은 별로 좋지 않다고 한다.

![](/assets/img/posts/30/11.png)

*Setter 주입*

\* Setter 주입은 누군가가 호출 했을 때, public으로 열려있어야 한다.

즉, 바꿀필요가 없는 소스가 public으로 노출이 된다는 단점이 있다. (중간에 잘못 바꾸면 문제가 생길 우려가 있다.)

![](/assets/img/posts/30/12.png)

*누구나 접근할 수 있는 상황*

결과적으로, 조립시점에 한번 사용하도록 하는 **'생성자 주입'** 이 가장 많이 쓰인다고 한다.

3\. 실무에서는 주로 정형화된 컨트롤러, 서비스, 리포지토리 같은 코드는 Component 스캔을 사용한다.

(정형화된 코드 : 일반적으로 작성되는 컨트롤러, 서비스, 리포지토리 코드들)

그리고 정형화 되지 않거나, **'상황에 따라 구현 클래스를 변경해야 하면 설정을 통해 스프링 빈으로 등록해야 한다'**

오늘 **가장 중요한 핵심**이다. 현재는 저장소가 선정되지 않은 상태이다. 그래서 Interface를 구현하는 MemoryMemberRepository를 나중에 다른 Repository를 바꿔치기해야 한다.

그 과정에서 **기존에 운영되는 코드들을 건드리지 않고** **한 번에 사용할 수 있도록** 하기 위해서는

**Bean을 직접 저장할 수 있는 방법을 알아야 한다.**

![](/assets/img/posts/30/13.png)![](/assets/img/posts/30/14.png)

*나중에 앞에 글자만 바꾸면 된다고 한다. (Memory -> DB)*

**\* 주의 : @Autowired를 통한 DI는 스프링이 관리하는 객체 안에서만 동작한다.**

**스프링 빈으로 등록하지 않고 내가 직****접 생성한 객체에서는 동작하지 않는다.**

**1) @Service 어노테이션을 붙여서 사용하거나**

**2) Configuration에 @Bean으로 Service를 등록하거나**

**이 2개가 안되어 있는 상황에서는 아래처럼 @Autowired를 작성해도 사용할 수 없다.**

![](/assets/img/posts/30/15.png)

*스프링 빈에 등록하고 나서야 @Autowired가 작동한다*
