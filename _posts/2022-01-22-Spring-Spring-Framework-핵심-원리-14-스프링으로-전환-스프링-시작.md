---
title: "[Spring] Spring Framework - 핵심 원리 (14) - 스프링으로 전환, 스프링 시작! + @Bean + @Configuration + 스프링 컨테이너 + 스프링 컨테이너 장점 + ApplicationContext + getBean() + 스프링 빈"
date: 2022-01-22 00:12:43 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["@bean", "@configuration", "annotationconfigapplicationcontext", "applicationcontext", "getbean()", "스프링 빈", "스프링 컨테이너", "스프링 컨테이너 장점", "스프링 핵심기능"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-14-%EC%8A%A4%ED%94%84%EB%A7%81%EC%9C%BC%EB%A1%9C-%EC%A0%84%ED%99%98-%EC%8A%A4%ED%94%84%EB%A7%81-%EC%8B%9C%EC%9E%91"
---
### **스프링으로 전환, 스프링 시작! + @Bean + @Configuration + 스프링 컨테이너 + 스프링 컨테이너 장점 + ApplicationContext + getBean() + 스프링 빈**

* * *

**지금까지는 '순수한 Java 코드'로 'DI'를 진행했다. 이제는 스프링의 기능을 사용해서 진행해보자!**

* * *

### **1\. AppConfig 설정 변경**

**아래 기존 순수 Java코드의 AppConfig를 '스프링'의 기능을 사용해서 변경하자.**

**변경 전**

```java
public class AppConfig {
	public MemberService memberSevice() {
		return new MemberServiceImpl(memberRepository());
	}
	public MemberRepository memberRepository() {
		return new MemoryMemberRepository();
	}
	public OrderService orderService() {
		return new OrderServiceImpl(memberRepository(), discountPolicy());
	}
	private DiscountPolicy discountPolicy() {
		return new FixDiscountPolicy();
	}
}
```

**변경 후**

```java
@Configuration
public class AppConfig {
	@Bean
	public MemberService memberSevice() {
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
	private DiscountPolicy discountPolicy() {
		return new FixDiscountPolicy();
	}
}
```

**@Configuration는 애플리케이션의 설정 정보를 담당하고 있는 기능을 맡고 있다.**

**@Bean는 각 메서드에 붙여주면, '스프링 컨테이너'에 '스프링 빈'으로 등록이 된다.**

### **2\. MemberApp**

**스프링을 사용하는 방법으로 변환해보자.**

**변경 전**

```java
public class MemberApp {
	public static void main(String[] args) {
		AppConfig appConfig = new AppConfig();
		MemberService memberService = appConfig.memberSevice();
		
		Member member = new Member(1L, "memberA", Grade.VIP);
		memberService.join(member);
		
		Member findMember = memberService.findMember(1L);
		System.out.println("new member = " + member.getName());
		System.out.println("find Member = " + findMember.getName());
	}
}
```

**가장 먼저 생성해야 하는 것은 'ApplicationContext'이다.** 

**스프링은 'ApplicationContext'로 시작을 하고 '스프링 컨테이너'이다. 모든 객체(Bean)를 관리한다.**

**'AnnotationConfigApplicationContext' 파라미터에 'AppConfig'을 넣어주면,**

**'AppConfig'안에 있는** **환경설정을 가지고 '스프링 컨테이너'에 담아서 관리를 하게끔 해준다.**

  
**기존에는 'AppConfig'에서 직접 찾아왔지만, 이제는 '스프링 컨테이너'를 통해서 찾아와야 한다.**

**변경 후**

```java
public class MemberApp {
	public static void main(String[] args
		ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
		MemberService memberService = applicationContext.getBean("memberService", MemberService.class);

		Member member = new Member(1L, "memberA", Grade.VIP);
		memberService.join(member);
		
		Member findMember = memberService.findMember(1L);
		System.out.println("new member = " + member.getName());
		System.out.println("find Member = " + findMember.getName());
	}
```

**'스프링 컨테이너'에 등록할 때는, 'Key : 메서드 이름, Value : 생성된 객체 인스턴스'이다.**

```java
@Bean
public MemberService memberService() { // Key
	return new MemberServiceImpl(memberRepository()); // Value
}
```

**꺼낼 때는 '컨테이너'에 '이름과 타입'을 주고 꺼내야 한다. (좌 : 꺼낼 '메서드' 이름 | 우 : '타입')**

```java
MemberService memberService = applicationContext.getBean("memberService", MemberService.class);
```

### **결과**

**아래 결과를 보게 되면, 10줄 중에서, 위 5줄은 '스프링' 내부에서 필요해서 등록한 'Bean'이다.**

**그리고 아래 5줄은 '@Bean'으로 등록해놓은 것들이 호출되는 것을 알 수 있다.**

**'MemberApp'과 'OrderApp'을 실행해보면 기존의 결과 동일하게 나오고 아래의 코드들이 추가되어 나온다.**

```java
[main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'org.springframework.context.annotation.internalConfigurationAnnotationProcessor'
[main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'org.springframework.context.event.internalEventListenerProcessor'
[main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'org.springframework.context.event.internalEventListenerFactory'
[main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'org.springframework.context.annotation.internalAutowiredAnnotationProcessor'
[main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'org.springframework.context.annotation.internalCommonAnnotationProcessor'
```

```java
[main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'appConfig'
[main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'memberService'
[main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'memberRepository'
[main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'orderService'
[main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'discountPolicy'
```

* * *

### **스프링 컨테이너** 

-   **'ApplicationContext'를 '스프링 컨테이너'라고 한다.**
-   **기존에는 개발자가 'AppConfig'를 사용해서 직접 객체를 생성하고 'DI'를 했지만, 이젠 '스프링 컨테이너'를 통해서 사용.**
-   **'스프링 컨테이너'는 '@Configuration'이 붙은 'AppConfig'를 설정(구성) 정보로 사용한다.**
-   **여기서 '@Bean'이라 적힌 메서드를 모두 호출해서 반환된 객체를 '스프링 컨테이너'에 등록한다.  
    이렇게 스프링 컨테이너에 등록된 객체를 '스프링 빈'이라 한다.**
-   **'스프링 빈'은 '@Bean'이 붙은 메서드의 명을 '스프링 빈'의 이름으로 사용한다.  
    (e.g : memberService, orderService)**
-   **이전에는 개발자가 필요한 객체를 'AppConfig'를 사용해서 직접 조회했지만, 이제부터는 스프링 컨테이너를 통해 필요한 '스프링 빈(객체)'를 찾아야 한다. '스프링 빈'은 'appliciationContext.getBean()'메서드를 사용해서 찾을 수 있다.**
-   **기존에는 개발자가 직접 자바 코드로 모든 것을 했다면, 이제부터는 '스프링 컨테이너'에 객체를 '스프링 빈'으로  
    등록하고, '스프링 컨테이너'에서 '스프링 빈'을 찾아서 사용하도록 변경되었다.**

### **코드가 약간 더 복잡해진 것 같은, '스프링 컨테이너'를 사용하면 어떤 장점이 있을까?**

**결론부터 말하면, 어마어마한 장점이 있다.**

**'스프링 컨테이너'가 관리해줌으로써 득이 될 것들이 엄청 많다.**

**다음 정리 때 더 자세하게 공부해보자!!**
