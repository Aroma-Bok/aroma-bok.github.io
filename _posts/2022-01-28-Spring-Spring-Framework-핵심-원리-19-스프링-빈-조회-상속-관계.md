---
title: "[Spring] Spring Framework - 핵심 원리 (19) - 스프링 빈 조회 (상속 관계)"
date: 2022-01-28 00:24:26 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["assertthat", "assertthrows", "isinstanceof", "스프링 기초 핵심", "스프링 빈 조회", "스프링 빈 조회 방법"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-19-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B9%88-%EC%A1%B0%ED%9A%8C-%EC%83%81%EC%86%8D-%EA%B4%80%EA%B3%84"
---
### **스프링 빈 조회 (상속관계)**

* * *

#### **스프링 빈 조회 - 상속 관계**

-   **부모 타입으로 조회하면, 자식 타입도 함께 조회한다.**
-   **그래서 모든 자바 객체의 최고 부모인 'Object'타입으로 조회하면, 모든 스프링 빈을 조회한다.**

![](/assets/img/posts/80/1.png)

**이 내용을 코드로 알아보자. 먼저 'ApplicationContextExtendsFindTest' 클래스를 만들자.**

**그리고 아래처럼 '@Configuration'을 하나 작성하자.**

```java
	public class ApplicationContextExtendsFindTest {
	AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);
    
    	@Configuration
	static class TestConfig{
		//빈의 이름이 다르지만, 객체 인스턴스 타입은 같을 수 있다.
		@Bean
		public DiscountPolicy rateDiscountPolicy() {
			return new RateDiscountPolicy();
		}
		@Bean
		public DiscountPolicy fixDiscountPolicy() {
			return new FixDiscountPolicy();
		}
	}
```

> **타입을 'DiscountPolicy'로 하는 이유**  
> **개발하거나 설계를 할 때, 역할과 구현을 항상 분리하자고 했었다. 'DiscountPolicy'을 보고 역할이 무엇인지 쉽게  
> 알아내고, 다른 곳에서'DI'를 할 때도 더 편리하게 사용할 수 있기 때문이다.** 

* * *

#### **1\. 부모 타입으로 조회 시, 자식이 둘 이상 있으면, 중복 오류가 발생하는 경우**

**'getBean()'으로 모든 클래스를 조회하면, 사용하려는 'DiscountPolicy'가 중복이 되기 때문에**

**'NoUniqueBeanDefinitionException'가 발생한다.** **'assertThrows()'를 이용해서 테스트를 작성하자.**

```java
    	@Test
	@DisplayName("부모 타입으로 조회시, 자식이 둘 이상 있으면, 중복 오류가 발생한다.")
	void findBeanByParentTypeDuplicate() {
		//DiscountPolicy bean = ac.getBean(DiscountPolicy.class);
		org.junit.jupiter.api.Assertions.assertThrows(NoUniqueBeanDefinitionException.class, 
				()-> ac.getBean(DiscountPolicy.class));
	}
```

* * *

#### **2\. 부모 타입으로 조회 시, 자식이 둘 이상 있으면, 빈 이름을 지정하여 조회하는 방법**

**'getBean()' 안에 'Bean'의 이름을 지정해줘서 특정 타입을 조회할 수 있다.**

```java
	@Test
	@DisplayName("부모 타입으로 조회시, 자식이 둘 이상 있으면, 빈 이름을 지정하면 된다..")
	void findBeanByParentTypeBeanName() {
		DiscountPolicy rateDiscountPolicy = ac.getBean("rateDiscountPolicy" ,DiscountPolicy.class);
		Assertions.assertThat(rateDiscountPolicy).isInstanceOf(RateDiscountPolicy.class);
	}
```

* * *

#### **3\. 특정 하위 타입으로 조회하는 방법**

**좋지 않은 방법이지만, 이런 방법도 있다고 생각하고 알아보자.**

**'DiscountPolicy' 대신에 특정 'RateDiscountPolicy'를 지정해서 조회하는 방법이 있다.** 

```java
	@Test
	@DisplayName("특정 하위 타입으로 조회") // 물론 좋지 않은 방법
	void findBeanBySubType() {
		RateDiscountPolicy bean = ac.getBean(RateDiscountPolicy.class);
		Assertions.assertThat(bean).isInstanceOf(RateDiscountPolicy.class);
	}
```

* * *

#### **4\. 부모 타입으로 모두 조회하기**

**'getBeansOfType'를 사용해서 'Key : Value'로 조회하는 방법이다.**

```java
	@Test
	@DisplayName("부모 타입으로 모두 조회하기")
	void findAllBeanByParentType() {
		Map<String, DiscountPolicy> beansOfType = ac.getBeansOfType(DiscountPolicy.class);
		Assertions.assertThat(beansOfType.size()).isEqualTo(2);
		for(String key : beansOfType.keySet()) {
			System.out.println("key = " + key + "value = " + beansOfType.get(key));
		}
	}
```

* * *

#### **5\. 부모 타입으로 모두 조회하기 - Object**

**모든 객체가 'Object'타입의 자식이기 때문에 **결과를 확인해보면 스프링이 내부적으로 쓰는 빈까지 조회된다.****

**조회의 기능을 거의 다 알아보았다. 사실, 'AppliContext'에서 'getBean'을 쓸 일이 거의 없다고 한다.**

**왜냐하면, ''스프링 컨테이너'가 자동으로 'DI'를 사용하는 방법을 사용하기 때문이다.**

  
**하지만, 가끔 '순수 Java Application'에서 컨테이너를 생성해서 쓸 일이 있기 때문에 알아두면 좋다고 한다.**

```java
	@Test
	@DisplayName("부모 타입으로 모두 조회하기 - Object")
	void findAllBeanObjectType() {
		Map<String,Object> beansOfType = ac.getBeansOfType(Object.class);
		for(String key : beansOfType.keySet()) {
			System.out.println("key = "+ key + "Value = " + beansOfType.keySet());
		}
	}
```

더보기

key = org.springframework.context.annotation.internalConfigurationAnnotationProcessorValue = \[org.springframework.context.annotation.internalConfigurationAnnotationProcessor, org.springframework.context.annotation.internalAutowiredAnnotationProcessor, org.springframework.context.annotation.internalCommonAnnotationProcessor, org.springframework.context.event.internalEventListenerProcessor, org.springframework.context.event.internalEventListenerFactory, applicationContextExtendsFindTest.TestConfig, rateDiscountPolicy, fixDiscountPolicy, environment, systemProperties, systemEnvironment, applicationStartup, org.springframework.context.annotation.ConfigurationClassPostProcessor.importRegistry, messageSource, applicationEventMulticaster, lifecycleProcessor\]  
key = org.springframework.context.annotation.internalAutowiredAnnotationProcessorValue = \[org.springframework.context.annotation.internalConfigurationAnnotationProcessor, org.springframework.context.annotation.internalAutowiredAnnotationProcessor, org.springframework.context.annotation.internalCommonAnnotationProcessor, org.springframework.context.event.internalEventListenerProcessor, org.springframework.context.event.internalEventListenerFactory, applicationContextExtendsFindTest.TestConfig, rateDiscountPolicy, fixDiscountPolicy, environment, systemProperties, systemEnvironment, applicationStartup, org.springframework.context.annotation.ConfigurationClassPostProcessor.importRegistry, messageSource, applicationEventMulticaster, lifecycleProcessor\]  
key = org.springframework.context.annotation.internalCommonAnnotationProcessorValue = \[org.springframework.context.annotation.internalConfigurationAnnotationProcessor, org.springframework.context.annotation.internalAutowiredAnnotationProcessor, org.springframework.context.annotation.internalCommonAnnotationProcessor, org.springframework.context.event.internalEventListenerProcessor, org.springframework.context.event.internalEventListenerFactory, applicationContextExtendsFindTest.TestConfig, rateDiscountPolicy, fixDiscountPolicy, environment, systemProperties, systemEnvironment, applicationStartup, org.springframework.context.annotation.ConfigurationClassPostProcessor.importRegistry, messageSource, applicationEventMulticaster, lifecycleProcessor\]  
key = org.springframework.context.event.internalEventListenerProcessorValue = \[org.springframework.context.annotation.internalConfigurationAnnotationProcessor, org.springframework.context.annotation.internalAutowiredAnnotationProcessor, org.springframework.context.annotation.internalCommonAnnotationProcessor, org.springframework.context.event.internalEventListenerProcessor, org.springframework.context.event.internalEventListenerFactory, applicationContextExtendsFindTest.TestConfig, rateDiscountPolicy, fixDiscountPolicy, environment, systemProperties, systemEnvironment, applicationStartup, org.springframework.context.annotation.ConfigurationClassPostProcessor.importRegistry, messageSource, applicationEventMulticaster, lifecycleProcessor\]  
key = org.springframework.context.event.internalEventListenerFactoryValue = \[org.springframework.context.annotation.internalConfigurationAnnotationProcessor, org.springframework.context.annotation.internalAutowiredAnnotationProcessor, org.springframework.context.annotation.internalCommonAnnotationProcessor, org.springframework.context.event.internalEventListenerProcessor, org.springframework.context.event.internalEventListenerFactory, applicationContextExtendsFindTest.TestConfig, rateDiscountPolicy, fixDiscountPolicy, environment, systemProperties, systemEnvironment, applicationStartup, org.springframework.context.annotation.ConfigurationClassPostProcessor.importRegistry, messageSource, applicationEventMulticaster, lifecycleProcessor\]  
key = applicationContextExtendsFindTest.TestConfigValue = \[org.springframework.context.annotation.internalConfigurationAnnotationProcessor, org.springframework.context.annotation.internalAutowiredAnnotationProcessor, org.springframework.context.annotation.internalCommonAnnotationProcessor, org.springframework.context.event.internalEventListenerProcessor, org.springframework.context.event.internalEventListenerFactory, applicationContextExtendsFindTest.TestConfig, rateDiscountPolicy, fixDiscountPolicy, environment, systemProperties, systemEnvironment, applicationStartup, org.springframework.context.annotation.ConfigurationClassPostProcessor.importRegistry, messageSource, applicationEventMulticaster, lifecycleProcessor\]  
key = rateDiscountPolicyValue = \[org.springframework.context.annotation.internalConfigurationAnnotationProcessor, org.springframework.context.annotation.internalAutowiredAnnotationProcessor, org.springframework.context.annotation.internalCommonAnnotationProcessor, org.springframework.context.event.internalEventListenerProcessor, org.springframework.context.event.internalEventListenerFactory, applicationContextExtendsFindTest.TestConfig, rateDiscountPolicy, fixDiscountPolicy, environment, systemProperties, systemEnvironment, applicationStartup, org.springframework.context.annotation.ConfigurationClassPostProcessor.importRegistry, messageSource, applicationEventMulticaster, lifecycleProcessor\]  
key = fixDiscountPolicyValue = \[org.springframework.context.annotation.internalConfigurationAnnotationProcessor, org.springframework.context.annotation.internalAutowiredAnnotationProcessor, org.springframework.context.annotation.internalCommonAnnotationProcessor, org.springframework.context.event.internalEventListenerProcessor, org.springframework.context.event.internalEventListenerFactory, applicationContextExtendsFindTest.TestConfig, rateDiscountPolicy, fixDiscountPolicy, environment, systemProperties, systemEnvironment, applicationStartup, org.springframework.context.annotation.ConfigurationClassPostProcessor.importRegistry, messageSource, applicationEventMulticaster, lifecycleProcessor\]  
key = environmentValue = \[org.springframework.context.annotation.internalConfigurationAnnotationProcessor, org.springframework.context.annotation.internalAutowiredAnnotationProcessor, org.springframework.context.annotation.internalCommonAnnotationProcessor, org.springframework.context.event.internalEventListenerProcessor, org.springframework.context.event.internalEventListenerFactory, applicationContextExtendsFindTest.TestConfig, rateDiscountPolicy, fixDiscountPolicy, environment, systemProperties, systemEnvironment, applicationStartup, org.springframework.context.annotation.ConfigurationClassPostProcessor.importRegistry, messageSource, applicationEventMulticaster, lifecycleProcessor\]  
key = systemPropertiesValue = \[org.springframework.context.annotation.internalConfigurationAnnotationProcessor, org.springframework.context.annotation.internalAutowiredAnnotationProcessor, org.springframework.context.annotation.internalCommonAnnotationProcessor, org.springframework.context.event.internalEventListenerProcessor, org.springframework.context.event.internalEventListenerFactory, applicationContextExtendsFindTest.TestConfig, rateDiscountPolicy, fixDiscountPolicy, environment, systemProperties, systemEnvironment, applicationStartup, org.springframework.context.annotation.ConfigurationClassPostProcessor.importRegistry, messageSource, applicationEventMulticaster, lifecycleProcessor\]  
key = systemEnvironmentValue = \[org.springframework.context.annotation.internalConfigurationAnnotationProcessor, org.springframework.context.annotation.internalAutowiredAnnotationProcessor, org.springframework.context.annotation.internalCommonAnnotationProcessor, org.springframework.context.event.internalEventListenerProcessor, org.springframework.context.event.internalEventListenerFactory, applicationContextExtendsFindTest.TestConfig, rateDiscountPolicy, fixDiscountPolicy, environment, systemProperties, systemEnvironment, applicationStartup, org.springframework.context.annotation.ConfigurationClassPostProcessor.importRegistry, messageSource, applicationEventMulticaster, lifecycleProcessor\]  
key = applicationStartupValue = \[org.springframework.context.annotation.internalConfigurationAnnotationProcessor, org.springframework.context.annotation.internalAutowiredAnnotationProcessor, org.springframework.context.annotation.internalCommonAnnotationProcessor, org.springframework.context.event.internalEventListenerProcessor, org.springframework.context.event.internalEventListenerFactory, applicationContextExtendsFindTest.TestConfig, rateDiscountPolicy, fixDiscountPolicy, environment, systemProperties, systemEnvironment, applicationStartup, org.springframework.context.annotation.ConfigurationClassPostProcessor.importRegistry, messageSource, applicationEventMulticaster, lifecycleProcessor\]  
key = org.springframework.context.annotation.ConfigurationClassPostProcessor.importRegistryValue = \[org.springframework.context.annotation.internalConfigurationAnnotationProcessor, org.springframework.context.annotation.internalAutowiredAnnotationProcessor, org.springframework.context.annotation.internalCommonAnnotationProcessor, org.springframework.context.event.internalEventListenerProcessor, org.springframework.context.event.internalEventListenerFactory, applicationContextExtendsFindTest.TestConfig, rateDiscountPolicy, fixDiscountPolicy, environment, systemProperties, systemEnvironment, applicationStartup, org.springframework.context.annotation.ConfigurationClassPostProcessor.importRegistry, messageSource, applicationEventMulticaster, lifecycleProcessor\]  
key = messageSourceValue = \[org.springframework.context.annotation.internalConfigurationAnnotationProcessor, org.springframework.context.annotation.internalAutowiredAnnotationProcessor, org.springframework.context.annotation.internalCommonAnnotationProcessor, org.springframework.context.event.internalEventListenerProcessor, org.springframework.context.event.internalEventListenerFactory, applicationContextExtendsFindTest.TestConfig, rateDiscountPolicy, fixDiscountPolicy, environment, systemProperties, systemEnvironment, applicationStartup, org.springframework.context.annotation.ConfigurationClassPostProcessor.importRegistry, messageSource, applicationEventMulticaster, lifecycleProcessor\]  
key = applicationEventMulticasterValue = \[org.springframework.context.annotation.internalConfigurationAnnotationProcessor, org.springframework.context.annotation.internalAutowiredAnnotationProcessor, org.springframework.context.annotation.internalCommonAnnotationProcessor, org.springframework.context.event.internalEventListenerProcessor, org.springframework.context.event.internalEventListenerFactory, applicationContextExtendsFindTest.TestConfig, rateDiscountPolicy, fixDiscountPolicy, environment, systemProperties, systemEnvironment, applicationStartup, org.springframework.context.annotation.ConfigurationClassPostProcessor.importRegistry, messageSource, applicationEventMulticaster, lifecycleProcessor\]  
key = lifecycleProcessorValue = \[org.springframework.context.annotation.internalConfigurationAnnotationProcessor, org.springframework.context.annotation.internalAutowiredAnnotationProcessor, org.springframework.context.annotation.internalCommonAnnotationProcessor, org.springframework.context.event.internalEventListenerProcessor, org.springframework.context.event.internalEventListenerFactory, applicationContextExtendsFindTest.TestConfig, rateDiscountPolicy, fixDiscountPolicy, environment, systemProperties, systemEnvironment, applicationStartup, org.springframework.context.annotation.ConfigurationClassPostProcessor.importRegistry, messageSource, applicationEventMulticaster, lifecycleProcessor\]

결과

![](/assets/img/posts/80/2.png)

> **참고로 출력하는 것은, 테스트를 위해서 '출력(println)'을 하는 것이다.**  
> **실제로 '테스트 코드'를 짤 때는 출력물을 남기면 안 된다.**  
> **왜냐면, 시스템이 자동 통과를 시스템이 결정하게 해야 한다. 즉, 눈으로 다 보고 있을 수 없기 때문이다.**  
> **실무에서는 테스트를 통과한다 안 한다로만, 평가할 수 있게끔만 코드를 짜야한다고 한다.**
