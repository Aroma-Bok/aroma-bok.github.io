---
title: "[Spring] Spring Framework - 핵심 원리 (21) - 다양한 설정 형식 방법 (Java 코드, XML 등 + AnnotationConfigApplicationContext / GenericXmlApplicationContext)"
date: 2022-02-04 08:23:00 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["annotationconfig", "annotationconfigapplicationcontext", "genericxmlapplicationcontext", "xml java 차이점", "xml 기반 bean 작성"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-21-%EB%8B%A4%EC%96%91%ED%95%9C-%EC%84%A4%EC%A0%95-%ED%98%95%EC%8B%9D-%EB%B0%A9%EB%B2%95-Java-%EC%BD%94%EB%93%9C-XML-%EB%93%B1-AnnotationConfigApplicationContext-GenericXmlApplicationContext"
---
### **다양한 설정 형식 방법 (Java 코드, XML 등 + AnnotationConfigApplicationContext / GenericXmlApplicationContext)**

* * *

#### **다양한 설정 형식 지원 - Java 코드, XML**

-   **스프링 컨테이너는 다양한 형식의 설정 정보를 받아들일 수 있게 유연하게 설계되어 있다.**
-   **Java, XML, Groovy 등등**

![](/assets/img/posts/84/1.png)

#### **AnnotationConfig - 어노테이션 기반 Java 코드 설정 사용**

-   **지금까지 했던 방법.**
-   **new AnnotationConfigApplicationContext(AppConfig.class)**
-   **AnnotationConfigApplicationContext 클래스를 사용하면서 Java 코드로 된 설정 정보를 넘기면 된다.**

#### **GenericXml - XML 설정 사용** 

-   **최근에는 스프링 부트를 많이 사용하면서 XML 기반의 설정은 잘 사용하지 않는다.**
-   **아직 많은 레거시 프로젝트 들이 xML로 되어 있고, 또 XML을 사용하면 컴파일 없이 빈 설정 정보를  
    변경할 수 있는장점도 있으므로 한 번쯤 배워두는 것도 괜찮다.**
-   **GenericXmlApplicationContext를 사용하면서 xml설정 파일을 넘기면 된다.**

**※ Java 파일이 아니면, resouces 폴더에 만들자.**

**※ Assertions (org.assertj.core.api)는 Test 폴더에 들어가 있지 않으면, 기능을 사용할 수 없다.**

**기존의 'Java 코드'와 'XML'을 비교해보면, 1:1 매핑되는 것은 비슷하다.** 

```java
@Configuration
public class AppConfig {
	// 나의 앱 전체를 설정하고 구성하는 역할을 가진 클래스
	@Bean
	public MemberService memberService() {
		//return new MemberServiceImpl(new MemoryMemberRepository()); //'MemoryMemberRepository'객체의 참조값을 'MemberServiceImpl'에 넣어준다.
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

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xsi:schemaLocation="http://www.springframework.org/schema/beans 
	http://www.springframework.org/schema/beans/spring-beans.xsd">
		
		
	<bean id="memberService" class="hello.core.member.MemberServiceImpl">
		<constructor-arg name="memberRepository" ref="memberRepository"></constructor-arg> 
		<!-- 생성자를 넘겨준다. -->
	</bean>
	
	<bean id="memberRepository" class="hello.core.member.MemoryMemberRepository"></bean>
	<!--'constructor-arg'에 'memberRepository'가 넘어간다. 그리고 구현체는 'MemoryMemberRepository'이다. -->
	
	
	<bean id="orderService" class="hello.core.order.OrderServiceImpl">
		<constructor-arg name="memberRepository" ref="memberRepository"></constructor-arg>
		<constructor-arg name="discountPolicy" ref="discountPolicy"></constructor-arg>
	</bean>
	
	<bean id="discountPolicy" class="hello.core.discount.RateDiscountPolicy"></bean>
		
</beans>
```

**XML 기반의 테스트 코드**

```java
public class XmlAppContext {
	
	@Test
	public void xmlAppContext() {
		ApplicationContext ac =  new GenericXmlApplicationContext("appConfig.xml");
		MemberService memberService =  ac.getBean("memberService", MemberService.class);
		Assertions.assertThat(memberService).isInstanceOf(MemberService.class);
	}
}
```

![](/assets/img/posts/84/2.png)

*결과*

-   **XML 기반의 appConfig.xml 스프링 설정 정보와 자바 코드로 된 AppConfig.java 설정 정보를** **비교해보면  
    거의 비슷하다는 것을 알 수 있다.**
-   **XML 기반으로 설정하는 것은 최근에 잘 사용하지 않으므로 이 정도로 마무리하고,  
    필요하면 스프링 공식 레퍼런스 문서를 확인해보자. \[ [https://spring.io/projects/spring-framework \]](https://spring.io/projects/spring-framework)**
