---
title: "[Spring] Spring Framework - 핵심 원리 (28) - 컴포넌트 스캔과 의존관계 자동 주입"
date: 2022-02-15 23:53:41 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["@autowired", "@component", "@componentscan", "@componentscan.filter", "컴포넌트 스캔", "컴포넌트 스캔과 의존관계 자동 주입"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-28-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8-%EC%8A%A4%EC%BA%94%EA%B3%BC-%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84-%EC%9E%90%EB%8F%99-%EC%A3%BC%EC%9E%85"
---
### **컴포넌트 스캔과 의존관계 자동 주입 + 컴포넌트 스캔  (@ComponentScan)+ @Component + @Autowired + **@ComponentScan. Filter****

* * *

#### **컴포넌트 스캔과 의존관계 자동 주입 시작하기**

**지금까지 스프링 빈을 등록할 때는 자바 코드의 @Bean이나 XML의 <bean>등을 통해서 설정 정보에 직접 등록할 스프링 빈을 나열했다.** 

**예제에서는 몇 개가 안되었지만, 이렇게 등록해야 할 스프링 빈이 수십, 수백 개가 되면 일일이 등록하기도**

**귀찮고, 설정 정보도 커지고, 누락하는 문제도 발생한다. 역시 개발자는 반복을 싫어한다.**

**그래서 스프링은 설정 정보가 없어도 자동으로 스프링 빈을 등록하는 컴포넌트 스캔이라는 기능을 제공한다. 또 의존 관계도 자동으로 주입하는 @Autowired라는 기능도 제공한다.**

#### **코드로 컴포넌트 스캔과 의존관계 자동 주입을 알아보자.**

> **참고: 컴포넌트 스캔을 사용하면 @Configuration이 붙은 설정 정보도 자동으로 등록되기 때문에 AppConfig, TestConfig 등 앞서 만들어 두었던 설정 정보도 함께 등록되고, 실행되어 버린다. 그래서 excludeFilters를 이용해서 설정 정보는 컴포넌트 스캔 대상에서 제외하자. 보통 설정 정보를 컴포넌트 스캔 대상에서 제외하지는 않지만, 기존 예제 코드를 최대한 남기고 유지하기 위해 이 방법을 선택했다.**

**컴포넌트 스캔 : 이름 그대로 @Component 어노테이션이 붙은 클래스를 스캔해서 스프링 빈으로 등록한다. @Component를 붙여주자.**

**※ @Configuration이 컴포넌트 스캔의 대상이 된 이유는 @Configuration 소스코드를 열어보면 @Component 어노테이션이 붙어있기 때문이다.**

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
```

#### **1\. AutoAppConfiguration 작성**

**excludeFilters = @ComponentScan. Filter ▶ 컴포넌트 스캔을 하면서, 제외할 것을 지정해주는 것**

  
**컴포넌트 스캔을 사용하려면 먼저 @Configuration 어노테이션을 설정 정보에 붙여주면 된다.**  
**코드를 보면 기존 AppConfig와는 다르게 @Bean으로 등록한 클래스가 하나도 없다.**

```java
@Configuration
@ComponentScan(
		excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class))
public class AutoAppConfig {
	
}
```

**이제 각 클래스가 컴포넌트 스캔의 대상이 되도록 @Component 어노테이션을 붙여주자.**

#### **2\. MemoryMemberRepository**

```java
@Component
public class MemoryMemberRepository implements MemberRepository
```

#### **3\. RateDiscountPolicy**

```java
@Component
public class RateDiscountPolicy implements DiscountPolicy
```

#### **4\. MemberServiceImpl**

```java
@Component
public class MemberServiceImpl implements MemberService
```

**그렇다면 의존관계는 어떻게 주입해야 할까?**

**기존의 AppConfig 코드를 보면 의존관계가 확실하게 명시되어있다.**

**아래 코드를 보면 memberRepository()가 의존관계로 들어가 있는 것을 확인할 수 있다.**

```java
	@Bean
	public MemberService memberService() {
		System.out.println("call AppConfig.memberService");
		return new MemberServiceImpl(memberRepository()); // --> 의존관계 주입!!!
	}
```

**그래서 자동 의존관계 주입이 필요하다. @Autowired를 사용하면 등록된 스프링 빈에서 같은 타입의 클래스를 찾아서 의존관계를 주입해준다. ac.getBean(MmberRepository.class)와 같은 기능이다.**

```java
	@Autowired
	public MemberServiceImpl(MemberRepository memberRepository) {
		this.memberRepository = memberRepository;
	}
```

**이전에 AppConfig에서는 @Bean으로 직접 설정 정보를 작성했고, 의존 관계도 직접 명시했다.**

**이제는 이런 설정 정보 자체가 없기 때문에, 의존관계 주입도 이 클래스 안에서 해결해야 한다.**

**@Autowired는 의존관계를 자동으로 주입해준다. 자세한 룰은 뒤에서 설명하겠다.**

#### **5\. OrderServiceImpl**

**OrderServiceImpl를 생성할 때, 스프링이 자동으로 ApplicationContext에서 MemberRepository와 DiscountPolicy를 주입해준다.**

```java
@ComponentScan
public class OrderServiceImpl implements OrderService{

	private final MemberRepository memberRepository;
	private final DiscountPolicy discountPolicy; 

	@Autowired
	public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
		super();
		this.memberRepository = memberRepository;
		this.discountPolicy = discountPolicy;
	}
```

**@Autowired를 사용하면 생성자에서 여러 의존 관계도 한 번에 주입받을 수 있다.**

**자 이제 테스트 코드를 작성해서 확인해보자.**

```java
public class AutoAppConfigTest {
	
	@Test
	public void basicScan() {
		AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AutoAppConfig.class);
		
		MemberService memberService = ac.getBean(MemberService.class);
		Assertions.assertThat(memberService).isInstanceOf(MemberService.class);
	}
}
```

![](/assets/img/posts/93/1.png)

*성공!*

**AnnotationConfigApplicationContext를 사용하는 것은 기존과 동일하다.**

**설정 정보로 AutoAppConfig 클래스를 넘겨준다.**

**실행해보면 기존과 같이 잘 동작하는 것을 확인할 수 있다.**

**그리고 콘솔을 잘 확인해보면, 컴포넌트 스캔이 동작하는 것을 확인할 수 있다.**

**빈으로 만들 후보를 정하고, 싱글톤 빈으로 만든다.**

```java
23:32:01.136 [main] DEBUG org.springframework.context.annotation.ClassPathBeanDefinitionScanner - Identified candidate component class: file [D:\EZEN08\MySpring Framework\core\bin\main\hello\core\discount\RateDiscountPolicy.class]
23:32:01.157 [main] DEBUG org.springframework.context.annotation.ClassPathBeanDefinitionScanner - Identified candidate component class: file [D:\EZEN08\MySpring Framework\core\bin\main\hello\core\member\MemberServiceImpl.class]
23:32:01.160 [main] DEBUG org.springframework.context.annotation.ClassPathBeanDefinitionScanner - Identified candidate component class: file [D:\EZEN08\MySpring Framework\core\bin\main\hello\core\member\MemoryMemberRepository.class]
23:32:01.194 [main] DEBUG org.springframework.context.annotation.ClassPathBeanDefinitionScanner - Identified candidate component class: file [D:\EZEN08\MySpring Framework\core\bin\main\hello\core\order\OrderServiceImpl.class]
23:32:01.660 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'autoAppConfig'
23:32:01.675 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'rateDiscountPolicy'
23:32:01.677 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'memberServiceImpl'
23:32:01.721 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'memoryMemberRepository'
```

* * *

### **컴포넌트 스캔과 자동 의존관계 주입이 어떻게 동작하는지 그림으로 알아보자.**

#### **1\. @ComponentScan**

![](/assets/img/posts/93/2.png)

 **@ComponentScan은 @Component가 붙은 모든 클래스를 스프링 빈으로 등록한다.**

**이때 스프링 빈의 기본 이름은 클래스명을 사용하되 맨 앞글자만 소문자를 사용한다.**

**빈 이름 기본 전략 : MemberServiceImpl 클래스 -> memberServiceImpl**

**빈 이름 직접 지정 : 만약 스프링 빈의 이름을 직접 지정하고 싶으면, @Component("memberService2") 이런 식으로 이름을 부여하면 된다.**

#### **2\. @Autowired 의존관계 자동 주입**

![](/assets/img/posts/93/3.png)

**생성자에 @Autowired를 지정하면, 스프링 컨테이너가 자동으로 해당 스프링 빈을 찾아서 주입한다.**

**이때 기본 조회 전략은 타입이 같은 빈을 찾아서 주입한다.**

**getBean(MemberRepository.class)와 동일하다고 이해하면 된다.** **더 자세한 내용은 뒤에서 설명하겠다.**

![](/assets/img/posts/93/4.png)

**생성자에 파라미터가 많아도 다 찾아서 자동으로 주입한다.**

* * *

### **정리**

**1\. @ComponentScan이 @Component가 붙은 클래스를 다 찾아서 스프링 빈으로 등록해준다.**

**2\. 의존관계를 주입하기 위해서 @Autowired를 사용해서 같은 타입을 기준으로 의존관계를 자동으로 주입**
