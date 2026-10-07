---
title: "[Spring] Spring Framework - 핵심 원리 (27) - @Configuration과 바이트코드 조작의 마법 + CGLIB + 스프링 싱글톤 원리 + @Autowired"
date: 2022-02-14 23:51:51 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["@configuration", "cglib", "스프링 싱글톤 원리", "싱글톤 원리"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-27-Configuration%EA%B3%BC-%EB%B0%94%EC%9D%B4%ED%8A%B8%EC%BD%94%EB%93%9C-%EC%A1%B0%EC%9E%91%EC%9D%98-%EB%A7%88%EB%B2%95-CGLIB-%EC%8A%A4%ED%94%84%EB%A7%81-%EC%8B%B1%EA%B8%80%ED%86%A4-%EC%9B%90%EB%A6%AC-Autowired"
---
### **@Configuration과 바이트코드 조작의 마법 + CGLIB + 스프링 싱글톤 원리 + @Autowired**

* * *

**스프링 컨테이너는 싱글톤 레지스트리다. 따라서 스프링 빈이 싱글톤이 되도록 바 장해주어야 한다.**

**그런데 스프링이 자바 코드까지 어떻게 하기는 어렵다. 자바 코드를 보면 분명 3번 호출되어야 하는 것이 맞다. 그래서 스프링은 클래스의 바이트코드를 조작하는 라이브러리를 사용한다.**

**모든 비밀은 @Configuration을 적용한 AppConfig에 있다.**

```java
	@Test
	public void configurationTest() {
		ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
		
		MemberServiceImpl memberService = ac.getBean("memberService",MemberServiceImpl.class);
		OrderServiceImpl orderService = ac.getBean("orderService",OrderServiceImpl.class);

		MemberRepository memberRepository = ac.getBean("memberRepository", MemberRepository.class);
		
		MemberRepository memberRepository1 = memberService.getMemberRepository();
		MemberRepository memberRepository2 = orderService.getMemberRepository();
		
		System.out.println("memberService -> memberRepository =" + memberRepository1);
		System.out.println("orederService -> memberRepository =" + memberRepository2);
		System.out.println("memberRepository =" + memberRepository);
		
		Assertions.assertThat(memberService.getMemberRepository()).isSameAs(memberRepository);
		Assertions.assertThat(orderService.getMemberRepository()).isSameAs(memberRepository);
	}
```

**▶ 위에는 저번 정리에서 사용했던 코드이다. memberRepository를 3번 호출하고 있다.**

**@Configuration의 기능을 확인하기 위해서 아래처럼 코드를 작성해서 추가하자.**

**사실, AnnotationConfigApplicationContext에 파라미터로 넘긴 값은 스프링 빈으로 등록된다.** 

**그래서 AppConfig도 스프링 빈이 된다. AppConfig 스프링 빈을 조회해서 클래스 정보를 출력해보자.**

```java
	@Test
	public void configurationDeep() {
		ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
		AppConfig bean = ac.getBean(AppConfig.class);
		
		System.out.println("bean = " + bean);
	}
```

**결과를 확인하면 아래처럼 AppConfig뒤에 $$가 추가되고 무언가 나온다.**

**순수한 클래스라면, class hello.core.AppConfig가 출력이 되어야 한다.**

**그렇다면 왜 이렇게 출력이 되었을까?**

```java
bean = hello.core.AppConfig$$EnhancerBySpringCGLIB$$96b67286@3829ac1
```

**예상과는 다르게 클래스명에 xxxCGLIB가 붙으면서 상당히 복잡해진 것을 볼 수 있다.** 

**이것은 내가 만든 클래스가 아니라 스프링이 CGLIB라는 바이트코드 조작 라이브러리를 사용해서 AppConfig 클래스를 상속받은 임의의 다른 클래스를 만들고, 그 다른 클래스를 스프링 빈으로 등록한 것이다!**

![](/assets/img/posts/92/1.png)

*AppConfig를 상속받은 다른 클래스를 만들어서 빈으로 등록한다!*

**그 임의의 다른 클래스가 바로 싱글톤이 보장되도록 해준다. 아마도 다음과 같이 바이트 코드를 조작해서 작성되어 있을 것이다. (실제로는 CGLIB의 내부 기술을 사용하는데 매우 복잡하다.)**

![](/assets/img/posts/92/2.png)

*AppConfig@CGLIB 예상 코드*

-   **@Bean이 붙은 메서드마다 이미 스프링 빈이 존재하면 존재하는 빈을 반환하고, 스프링 빈이 없으면 생성해서  
    스프링 빈으로 등록하고 반환하는 코드가 동적으로 만들어진다.**
-   **덕분에 싱글톤이 보장되는 것이다.**

> **참고 : AppConfig@CGLIB는 AppConfig의 자식 타입이므로, AppConfig 타입으로 조회할 수 있다.**

**@Configuration을 적용하지 않고, @Bean만 적용하면 어떻게 될까?**

**@Configuration을 붙이면 바이트코드를 조작하는 CGLIB 기술을 사용해서 싱글톤을 보장하지만,**

**만약 @Bean만 적용하면 어떻게 될까?**

**AppConfig에서 @Configuration을 주석 처리하고 다시 실행해보자.** 

**결과를 확인하면, AppConfig가 CGLIB 기술 없이 순수한 AppConfig로 스프링 빈에 등록된 것을 확인할 수 있다.**

```java
bean = hello.core.AppConfig@2f4205be
```

**하지만, memberRepository가 3번 호출되는 것을 확인할 수 있다.** 

**1번은 @Beansdp 의해서 스프링 컨테이너에 등록하기 위해서이고, 2번은 각각 memberRepository()를 호출하면서 발생한 코드다.**

```java
call AppConfig.memberService
call AppConfig.memberRepository
call AppConfig.memberRepository
call AppConfig.orderService
call AppConfig.memberRepository
```

**즉, 싱글톤이 깨진 것이다. 그리고 인스턴스가 같은지 테스트하는 코드도 실패하고,**

**각각 다른 MemoryMemberRepository 인스턴스를 가지고 있다.**

```java
memberService -> memberRepository =hello.core.member.MemoryMemberRepository@5ee2b6f9
orederService -> memberRepository =hello.core.member.MemoryMemberRepository@23d1e5d0
memberRepository =hello.core.member.MemoryMemberRepository@704f1591
```

![](/assets/img/posts/92/3.png)

*다 다른 인스턴스로 인해 테스트 실패!*

**그리고 문제가 하나 더 있다.**

**아래처럼 memberRepository를 원래 빈으로 등록하기 위해서 코드를 작성했었다.**

**하지만 @Configuration을 지워버리면, 더 이상 스프링 컨테이너가 관리하지 않는 객체이다.**

**즉, private final MemberRepository memberRepository = new MemoryMemberRepository(); 와**

**똑같은 코드가 된다.** **(OrderServiceImpl에도 등록한 객체도 마찬가지이다.)**

```java
public class MemberServiceImpl implements MemberService{

	private final MemberRepository memberRepository;
```

**물론, 이러한 문제는 나중에 공부할 @Autowired라는 의존관계 주입 어노테이션을 사용하면 문제가 해결될 수 있다.**

#### **정리**

-   **@Bean만 사용해도 스프링 빈으로 등록되지만, 싱글톤을 보장하지 않는다.**
-   **memberRepository()처럼 의존관계 주입이 필요해서 메서드를 직접 호출할 때 싱글톤을 보장하지 않는다.**
-   **크게 고민할 것 없다. 스프링 설정 정보는 항상 @Configuration을 사용하자.**
