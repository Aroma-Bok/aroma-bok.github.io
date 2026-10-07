---
title: "[Spring] Spring Framework - 핵심 원리 (16) - 컨테이너에 등록된 모든 빈 조회 + 등록된 빈(Bean) 확인 법 + getBeanDefinitionNames() + getBean() + getBeanDefinition() + ROLE_APPLICATION + ROLE_INFRASTRUCTURE"
date: 2022-01-24 23:59:28 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["getbean", "getbeandefinition", "getbeandefinitionnames", "role_application", "role_infrastructure", "등록된 빈 출력법", "등록된 빈 확인", "빈 등록 확인법"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-16-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EC%97%90-%EB%93%B1%EB%A1%9D%EB%90%9C-%EB%AA%A8%EB%93%A0-%EB%B9%88-%EC%A1%B0%ED%9A%8C-%EB%93%B1%EB%A1%9D%EB%90%9C-%EB%B9%88Bean-%ED%99%95%EC%9D%B8-%EB%B2%95-getBeanDefinitionNames-getBean-getBeanDefinition-ROLEAPPLICATION-ROLEINFRASTRUCTURE"
---
## **컨테이너에 등록된 모든 빈 조회 + 등록된 빈(Bean) 확인 법 + getBeanDefinitionNames() + getBean() + getBeanDefinition() + ROLE\_APPLICATION + ROLE\_INFRASTRUCTURE**

* * *

**'스프링 컨테이너'에 실제 '스프링 빈'들이 잘 등록되어 있는지 확인해보자.**

### **1\. 빈(Bean)을 조회할 클래스 생성**

```java
public class ApplicationContextInfoTest {
	
	AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
	@Test
	@DisplayName("모든 빈 출력하기")
	void findAllBean() { // 참고 : 'Junit5'부터는 'void' 앞에 'public' 생략 가능
		String[] beanDefinitionNames =  ac.getBeanDefinitionNames();
		// 이름을 꺼낸다.
		
		for(String beanDefinitionName : beanDefinitionNames) {
			Object bean = ac.getBean(beanDefinitionName);
			// 타입을 모르기 때문에 'Object'로 꺼내진다.
			System.out.println("name : " + beanDefinitionName + "object : " + bean);
		}
	}
```

**'모든 빈(Bean)' 출력하기**

-   **실행하면 '스프링' 등록된 '모든 빈(Bean)'정보를 출력할 수 있다.**
-   **'getBeanDefinitionNames()' : 스프링에 등록된 모든 빈 이름을 조회한다.**
-   **'getBean()' : 빈 이름으로 빈 객체(인스턴스)를 조회한다.**

**결과를 보면 총 10줄의 빈(Bean)을 확인할 수 있다.**

**아래 5줄은 '스프링' 내부적으로 확장하려고 쓰는 '빈(Bean)'들이다.**

```java
name : org.springframework.context.annotation.internalConfigurationAnnotationProcessorobject : org.springframework.context.annotation.ConfigurationClassPostProcessor@11eadcba
name : org.springframework.context.annotation.internalAutowiredAnnotationProcessorobject : org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor@4721d212
name : org.springframework.context.annotation.internalCommonAnnotationProcessorobject : org.springframework.context.annotation.CommonAnnotationBeanPostProcessor@1b065145
name : org.springframework.context.event.internalEventListenerProcessorobject : org.springframework.context.event.EventListenerMethodProcessor@45cff11c
name : org.springframework.context.event.internalEventListenerFactoryobject : org.springframework.context.event.DefaultEventListenerFactory@207ea13
```

**아래 5줄이 직접 등록한 '빈(Bean)'이다.**

```java
name : appConfigobject : hello.core.AppConfig$$EnhancerBySpringCGLIB$$b307e9d0@4bff1903
name : memberServiceobject : hello.core.member.MemberServiceImpl@62dae540
name : memberRepositoryobject : hello.core.member.MemoryMemberRepository@5827af16
name : orderServiceobject : hello.core.order.OrderServiceImpl@654d8173
name : discountPolicyobject : hello.core.discount.FixDiscountPolicy@56c9bbd8
```

**만약에 직접 등록한 '빈(Bean)'들만 보고 싶다면, 아래처럼 'for문'과 'if문'을 사용해서 코드를 작성해보자.**

```java
	@Test
	@DisplayName("애플리케이션 빈 출력하기")
	void findApplicationBean() { // 참고 : 'Junit5'부터는 'void' 앞에 'public' 생략 가능
		String[] beanDefinitionNames =  ac.getBeanDefinitionNames();
		
		for(String beanDefinitionName : beanDefinitionNames) {
			BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);
			
			if(beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION) {
				Object bean = ac.getBean(beanDefinitionName);
				// 타입을 모르기 때문에 'Object'로 꺼내진다.
				System.out.println("name : " + beanDefinitionName + "object : " + bean);
```

**'getBeanDefinition()' : 빈(Bean)에 대한 'meta data' 정보들을 반환한다.**

**Role - ROLE\_APPLICATION : 직접 등록한 애플리케이션 빈  
Role - ROLE\_INFRASTRUCTURE : 스프링이 내부에서 사용하는 빈**

**즉, 반환한 정보가 'ROLE\_APPLICATION '와 같다면, 해당하는 '빈(Bean)'들만 조회한다.**

**결과를 확인해보면 아래처럼 나오는 것을 볼 수 있다.**

**(반대로 자체적으로 확장한 '빈(Bean)'을 보고 싶다면 'ROLE\_INFRASTRUCTURE'을 사용하면 된다.)**

```java
name : appConfigobject : hello.core.AppConfig$$EnhancerBySpringCGLIB$$b307e9d0@11eadcba
name : memberServiceobject : hello.core.member.MemberServiceImpl@4721d212
name : memberRepositoryobject : hello.core.member.MemoryMemberRepository@1b065145
name : orderServiceobject : hello.core.order.OrderServiceImpl@45cff11c
name : discountPolicyobject : hello.core.discount.FixDiscountPolicy@207ea13
```
