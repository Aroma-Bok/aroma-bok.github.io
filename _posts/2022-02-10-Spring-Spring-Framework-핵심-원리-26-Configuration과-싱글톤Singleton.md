---
title: "[Spring] Spring Framework - 핵심 원리 (26) - @Configuration과 싱글톤(Singleton)"
date: 2022-02-10 23:42:08 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["@configuration", "스프링", "싱글톤 보장"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-26-Configuration%EA%B3%BC-%EC%8B%B1%EA%B8%80%ED%86%A4Singleton"
---
### **@Configuration과 싱글톤(Singleton)**

* * *

**@Configuration은 사실 싱글톤을 위해서 존재하는 것이다.**

**아래 코드를 보자.**

**memberService를 호출하면, MemberServiceImpl을 호출하면서 memberRepository를 호출한다.**

**@Bean memberService -> new MemoryMemberRepository()**  
**@Bean orderService  -> new MemoryMemberRepository() 호출이 2번 일어난다.**

**그렇다면 이러면 싱글톤이 깨지는 거 아닐까??** 

```java
@Configuration
public class AppConfig {
	@Bean
	public MemberService memberService() {
		//return new MemberServiceImpl(new MemoryMemberRepository());
		return new MemberServiceImpl(memberRepository());
	}
	@Bean
	public MemberRepository memberRepository() {
		return new MemoryMemberRepository();
	}
	@Bean
	public OrderService orderService() {
		return new OrderServiceImpl(memberRepository(), discountPolicy());
	}
	@Bean
	public DiscountPolicy discountPolicy() {
		return new FixDiscountPolicy();
		//return new RateDiscountPolicy();
	}
}
```

**결과적으로, 각각 다른 2개의 MemoryMemberRepository가 생성되면서 싱글톤이 깨지는 것처럼 보인다.**

**스프링 컨테이너는 이 문제를 어떻게 해결할까?**

**확인하기 위해서, MemberServiceImpl과 OrderServiceImpl에 아래 코드를 추가해서 확인해보자.**

```java
	//테스트 용도
	public MemberRepository getMemberRepository() {
		return memberRepository;
	}
```

**그리고 테스트 코드를 작성해서 실행해보자.**

```java
public class ConfigurationSingletonTest {
	
	@Test
	public void configurationTest() {
		ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
		
		MemberServiceImpl memberService = ac.getBean("memberService",MemberServiceImpl.class);
		OrderServiceImpl orderService = ac.getBean("orderService",OrderServiceImpl.class);
		// 참고 :  원래는 이렇게 구체 타입으로 꺼내면 좋지 않다.
		MemberRepository memberRepository = ac.getBean("memberRepository", MemberRepository.class);
		
		MemberRepository memberRepository1 = memberService.getMemberRepository();
		MemberRepository memberRepository2 = orderService.getMemberRepository();
		
		System.out.println("memberService -> memberRepository =" + memberRepository1);
		System.out.println("orederService -> memberRepository =" + memberRepository2);
		System.out.println("memberRepository =" + memberRepository);
		
		Assertions.assertThat(memberService.getMemberRepository()).isSameAs(memberRepository);
		Assertions.assertThat(orderService.getMemberRepository()).isSameAs(memberRepository);
		
	}
}
```

```java
memberService -> memberRepository =hello.core.member.MemoryMemberRepository@6bb75258
orederService -> memberRepository =hello.core.member.MemoryMemberRepository@6bb75258
memberRepository =hello.core.member.MemoryMemberRepository@6bb75258
```

![](/assets/img/posts/90/1.png)

**결과는 똑같은 객체를 생성한다!!!! 그리고 테스트까지 통과!!**

**왜 이럴까? 호출을 3번씩이나 하는데 똑같은 인스턴스를 조회하고 있다.**

**확인해보면 memberRepository 인스턴스는 모두 같은 인스턴스가 공유되어 사용된다.**

**AppConfig의 자바 코드를 보면 분명히 각각 3번 new MemoryRepositry를 호출해서**

**다른 인스턴스가 생성되어야 하는데??**

**어떻게 된 일일까? 혹시 세 번 호출이 안 되는 것일까?** 

**출력 코드를 넣어서 확인해보자. 'System.out.println("call AppConfig.memberService");'를 추가하자.**

```java
@Configuration
public class AppConfig {
	
	@Bean
	public MemberService memberService() {
		//return new MemberServiceImpl(new MemoryMemberRepository()); //'MemoryMemberRepository'객체의 참조값을 'MemberServiceImpl'에 넣어준다.
		System.out.println("call AppConfig.memberService");
		return new MemberServiceImpl(memberRepository());
	}
	@Bean
	public MemberRepository memberRepository() {
		System.out.println("call AppConfig.memberService");
		return new MemoryMemberRepository();
	}
	@Bean
	public OrderService orderService() {
		System.out.println("call AppConfig.memberService");
		return new OrderServiceImpl(memberRepository(), discountPolicy());
	}
	@Bean
	public DiscountPolicy discountPolicy() {
		return new FixDiscountPolicy();
		//return new RateDiscountPolicy();
	}
}
```

**결과적으로 memberRepository가 3번 호출이 되어야 한다.** **아래 결과를 보면 호출은 3번이 된다.**

**즉, 정상적으로 돌아가고 있다.**

```java
call AppConfig.memberService
23:37:07.037 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'memberRepository'
call AppConfig.memberService
23:37:07.044 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'orderService'
call AppConfig.memberService
23:37:07.048 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'discountPolicy'
memberService -> memberRepository =hello.core.member.MemoryMemberRepository@6bb75258
orederService -> memberRepository =hello.core.member.MemoryMemberRepository@6bb75258
memberRepository =hello.core.member.MemoryMemberRepository@6bb75258
```

**여기서 느낄 것이다. 스프링이 싱글톤을 보장을 해주고 있구나!**

**다음 정리 때 자세하게 정리해보자.**
