---
title: "[Spring] Spring Framework - 핵심 원리 (17) - 스프링 빈 조회 + 스프링 컨테이너에서 빈 찾는 방법 + getBean() + assertThrows + assertThat + NoSuchBeanDefinitionException"
date: 2022-01-25 23:56:35 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["assertions", "assertthat", "assertthrows", "nosuchbeandefinitionexception", "람다함수 사용법", "테스트 코드"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-17-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B9%88-%EC%A1%B0%ED%9A%8C-%EC%8A%A4%ED%94%84%EB%A7%81-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EC%97%90%EC%84%9C-%EB%B9%88-%EC%B0%BE%EB%8A%94-%EB%B0%A9%EB%B2%95-getBean-assertThrows-assertThat-NoSuchBeanDefinitionException"
---
## **스프링 빈 조회 + 스프링 컨테이너에서 빈 찾는 방법 + getBean() + assertThrows + assertThat + **NoSuchBeanDefinitionException****

* * *

**'스프링 컨테이너'에서 '스프링 빈'을 찾는 가장 기본적인 조회 방법**

-   **getBean("빈 이름", 타입)**
-   **getBean(타입)**
-   **조회 대상 '스프링 빈'이 없으면 예외 발생**  
    **\=> NoSuchBeanDefinitionException: No bean named 'xxxx' available**

#### **1\. 빈 이름으로 조회 테스트**

```java
public class ApplicationContextBasicFindTest {

	AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
	
	@Test
	@DisplayName("빈 이름으로 조회")
	void findBeanByName() {
		MemberService memberService = ac.getBean("memberService",MemberService.class);
		System.out.println("memberService = " + memberService);
		System.out.println("memberService.getClass() = " + memberService.getClass());
	}
}
```

**결과**

```java
memberService = hello.core.member.MemberServiceImpl@efde75f
memberService.getClass() = class hello.core.member.MemberServiceImpl
```

#### **2\. 이름 없이 타입으로만 조회 테스트**

**'Assertions - (org.assertj.core.api)'을 이용하여 클래스 타입이 동일한지 검증하는 작업 추가**

```java
public class ApplicationContextBasicFindTest {

	AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
	
	@Test
	@DisplayName("빈 이름으로 조회")
	void findBeanByName() {
		MemberService memberService = ac.getBean("memberService",MemberService.class);
		System.out.println("memberService = " + memberService);
		System.out.println("memberService.getClass() = " + memberService.getClass());
		
		Assertions.assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
	}
}
```

![](/assets/img/posts/78/1.png)

*결과*

#### **3\. 구체화 타입으로 조회 테스트**

**역할과 구현을 구분하는 개념에서는 좋은 테스트 코드가 아니다.**

**지금 코드는 구현에 구현에 의존한 것이다.** **즉, 유연성이 떨어진다.**

**하지만, 개발을 하는 과정에서는 어떤 일이 생길지 모르기 때문에 이렇게 써먹는 방법도 알아두자.**

```java
	@Test
	@DisplayName("구체 타입으로 조회") 
	void findBeanByName2() {
		MemberServiceImpl memberService = ac.getBean("memberService",MemberServiceImpl.class);
		Assertions.assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
	}
```

#### **4\. 빈 이름으로 조회가 안 되는 테스트**

**"XXXX"라는 '빈(Bean)'의 이름이 없기 때문에 에러가 발생해야 정상이다.**

**찾고자 하는 빈이 없을 때 발생하는 에러는 'NoSuchBeanDefinitionException'이 발생한다.**

```java
	@Test
	@DisplayName("빈 이름으로 조회x")
	void findBeanByNameX() {
		MemberService memberService = ac.getBean("XXXX",MemberService.class);
		Assertions.assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
	}
```

![](/assets/img/posts/78/2.png)

*결과*

**에러를 발생시켜야 성공하는 검증 방법은 아래와 같다.**

**오른쪽의 '람다 함수()'가 발생하면, 왼쪽의 에러가 발생해야 정상인 테스트를 만드는 코드다.  
**

**'Assertions - (org.junit.jupiter.api)'의 'assertThrows' 기능을 사용  
**

```java
	@Test
	@DisplayName("빈 이름으로 조회x")
	void findBeanByNameX() {
		org.junit.jupiter.api.Assertions.assertThrows(NoSuchBeanDefinitionException.class,
        () -> ac.getBean("XXXX",MemberService.class));
	}
```

![](/assets/img/posts/78/3.png)

*결과*
