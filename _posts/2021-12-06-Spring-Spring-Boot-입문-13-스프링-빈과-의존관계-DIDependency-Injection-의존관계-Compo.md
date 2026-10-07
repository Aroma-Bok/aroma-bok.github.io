---
title: "[Spring] Spring Boot - 입문 (13) 스프링 빈과 의존관계 + DI(Dependency Injection) 의존관계 + Component Scan  + 싱글톤 + 스프링 빈"
date: 2021-12-06 23:36:16 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["component scan", "di", "스프링 부트", "스프링 부트 기본", "스프링 빈", "싱글톤", "의존관계", "의존성 주입", "컴포넌트"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Boot-%EC%9E%85%EB%AC%B8-13-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B9%88%EA%B3%BC-%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84-DIDependency-Injection-%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84-Component-Scan-%EC%8B%B1%EA%B8%80%ED%86%A4-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B9%88"
---
#### **스프링 빈과 의존관계 + DI(Dependency Injection) + Component Scan  + 싱글톤 + 스프링 빈**

* * *

화면을 붙이고 싶다 = 뷰 템플릿 + 컨트롤러 필요하는 의미

멤버 컨트롤러 -> 멤버 서비스를 통해서 조회를 하고 회원가입을 시켜야 한다 == 의존관계가 있다는 의미이다.

```java
@Controller
public class MemberController {
	
	private final MemberService memberService = new MemberService();

}
```

  
**스프링 컨테이너**라는 통이 생기는데, 컨트롤러라는 어노테이션이 있으면, MemberController로 객체를 생성해서  
스프링에 넣어둔다. 그리고 스프링이 관리한다.

  
MemberService를 가져다 써야할 때, 이렇게 new를 통해서 생성할 수 도 있지만,  
스프링이 관리를 하게되면, 스프링 컨테이너에 계속 등록하고 받아서 쓰게 된다.

  
다른 컨트롤러에서도 이렇게 계속 **New를 통해서 생성해서 사용하면**, 계속 새로 만들어야하 한다. 즉, **낭비**다.

그래서 이제는 컨테이너에 **단순하게 등록**하고 쓰자!

```java
public class MemberController {

	private final MemberService memberService;

	@Autowired // 스프링 뜰 때, 생성자 호출. => @Autowired되어 있으면, 스프링이 MemberService를 가져다가 연결 시켜준다.
	public MemberController(MemberService memberService) {
		super();
		this.memberService = memberService;
	}
	
}
```

**@Autowired**되어 있으면, 스프링 뜰 때, 생성자 호출하면서 스프링이 MemberService를 가져다가 연결시켜준다.

하지만, 결과는?? 실패!! Why? 

```java
***************************
APPLICATION FAILED TO START
***************************

Description:

Parameter 0 of constructor in hello.hellospring.controller.MemberController required a bean of type 'hello.hellospring.service.MemberService' that could not be found.

Action:

Consider defining a bean of type 'hello.hellospring.service.MemberService' in your configuration.
```

이유는 Controller는 **@Controller 이런 식으로** **어노테이션을 붙여서 스프링이 인지**할 수 있게 만들었지만,

Service는 그게 아니라서 **Service에다가도 어노테이션을 붙여줘야 한다.**

그리고 Service에서는 MemberRepository와 의존관계 이기 때문에, MemberRepository에도

**@Repository 어노테이션을 붙여서 스프링이 인지할 수 있게끔 만들어** 줘야 한다.

**\+ 구현체에도 @Repository를 붙여줘야 한다.**

![](/assets/img/posts/29/1.png)![](/assets/img/posts/29/2.png)

*Before -> After*

![](/assets/img/posts/29/3.png)

![](/assets/img/posts/29/4.png)

*구현체*

**설명**

![](/assets/img/posts/29/5.png)

1\. Controller & Service 연결

: 생성자에 @Autowired를 쓰면, Controller가 생성이 될 때, 스프링 빈에 등록되어 있는 Service객체를 갖고 와서 Controller에 넣어준다. 이게 바로, **'의존관계 주입 (DI)'이다.** \[스프링이 넣어준 것\]

2\. Service & Repository 연결 

: Service에서 필요한 Repository와 연결하면서 @Autowired를 사용하여, Service가 생성이 될 때, Repository 구현체를 갖고 와서 Service에 넣어준다. 이것도 **'의존관계 주입 (DI)'이다**

![](/assets/img/posts/29/6.png)

*구현체*

결과 : 성공!

![](/assets/img/posts/29/7.png)

**스프링 빈을 등록하는 2가지 방법**

**1\. 컴포넌트 스캔과 자동 의존관계 설정 :**

@Autowired , @Service ,@Repository를 사용하면서 등록하는 방법 (위에서 한 방법)

\[**Why? 컴포넌트 스캔?** : Service 안에서 @Service가 아니라, @Component도 된다. (다른 것도 마찬가지)

**@Service와 @Controller, @Repository 안에 들어가 보면, @Component가 등록이 되어있다.**

![](/assets/img/posts/29/8.png)![](/assets/img/posts/29/9.png)![](/assets/img/posts/29/10.png)

이래서 Component 스캔이라 한다. 

**@Autowired는 각 해당의 연관관계를 의미**한다. 즉, **이어주는 역할**이다.

이렇게 써야 스프링 빈에 대한 이점이 많다고 한다. 이점은 나중에 설명해주신다고 한다.

![](/assets/img/posts/29/11.png)

**Q: 그럼 아무 데나 @Component, @Service를 넣으면 되나?**

**A: 안된다. 실행시키는 애플리케이션이 해당된 Package를 기준으로 다 뒤져서 스프링이 확인해서 스프링 빈으로 등록한다고 한다. 즉, 다른 패키지이면 스캔을 안 한다고 한다. (설정을 해주면 되지만, 기본적으로는 안된다고 한다.)**

**참고** 

**스프링은 스프링 컨테이너에 스프링 빈을 등록할 때, 기본으로 싱글톤으로 등록한다. (싱글톤으로 공유)**

**즉, 유일하게 하나만 등록한다는 뜻. (특별한 경우를 제외하면 기본은 싱글톤)**

2\. 자바 코드로 직접 스프링 빈 등록 : 이건 다음 정리에.....
