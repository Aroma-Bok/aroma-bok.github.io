---
title: "[Spring] Spring Framework - 핵심 원리 (18) - 동일한 타입의 스프링 빈을 조회 하는 방법"
date: 2022-01-26 23:38:40 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["getbean", "getbeansoftype", "nouniquebeandefinitionexception", "동일한 타입의 스프링 빈을 조회 하는 방법"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-18-%EB%8F%99%EC%9D%BC%ED%95%9C-%ED%83%80%EC%9E%85%EC%9D%98-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B9%88%EC%9D%84-%EC%A1%B0%ED%9A%8C-%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95"
---
## 동일한 타입의 스프링 빈을 조회하는 방법 + getBean + getBeansOfType + NoUniqueBeanDefinitionException

* * *

**타입으로 조회 시 같은 타입의 스프링 빈이 둘 이상이면 오류가 발생한다. 이때는 빈 이름을 지정하자.**

**'getBeansOfType()'을 사용하면 해당 타입의 모든 빈을 조회할 수 있다.**

#### **1\. 타입으로 조회 시 같은 타입이 둘 이상 있으면, 중복 오류가 발생하는 경우**

**새롭게 테스트 클래스를 만들어서 아래처럼 작성해보자.**

**그리고 테스트 코드에서만 사용할 'SameBeanConfig' 설정 파일 클래스를 만들어주자.**

**'SameBeanConfig'안에 '빈(Bean)'의 이름은 다르지만 같은 '객체 타입'으로 리턴해주도록 '빈(Bean)'을**

**등록하고 테스트를 실행해보자.**

```java
public class ApplicationContextSameBeanFindTest {

	AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(SameBeanConfig.class);
	
	@Test
	@DisplayName("타입으로 조회시 같은 타입이 둘 이상 있으면, 중복 오류가 발생한다.")
	void findBeanByTypeDuplicate() {
		MemberRepository bean = ac.getBean(MemberRepository.class);
	}
    
	@Configuration
	static class SameBeanConfig{
		//빈의 이름이 다르지만, 객체 인스턴스 타입은 같을 수 있다.
		@Bean
		public MemberRepository memberRepository1() {
			return new MemoryMemberRepository();
		}
		@Bean
		public MemberRepository memberRepository2() {
			return new MemoryMemberRepository();
		}
	}
}
```

![](/assets/img/posts/79/1.png)

*결과 : 에러*

**결과는 'NoUniqueBeanDefinitionException'가 발생한다. 말 그대로 빈이 유일하지 않아서 발생한 에러다.**

**'Assertions - org.junit.jupiter.api'의 'assertThrows'를 사용해서 성공하도록 수정해보자.**

```java
	@Test
	@DisplayName("타입으로 조회시 같은 타입이 둘 이상 있으면, 중복 오류가 발생한다.")
	void findBeanByTypeDuplicate() {
		org.junit.jupiter.api.Assertions.assertThrows(NoUniqueBeanDefinitionException.class, 
				() -> ac.getBean(MemberRepository.class));
	}
```

#### **2\. 타입으로 조회 시 같은 타입이 둘 이상 있으면, 빈 이름을 지정하여 해결하는 방법**

**앞서 배웠던 방법이다. 'getBean()'을 사용할 때, '클래스 타입'만 지정하지 말고, '빈의 이름'까지 입력해서 조회하자.**

```java
	@Test
	@DisplayName("타입으로 조회시 같은 타입이 둘 이상 있으면, 빈 이름을 지정하면 된다.")
	public void findBeanByName() {
		MemberRepository memberRepository = ac.getBean("memberRepository1",MemberRepository.class);
		Assertions.assertThat(memberRepository).isInstanceOf(MemberRepository.class);
	}
```

#### **3\. 특정 타입을 모두 조회하기**

**'getBeansOfType()'을 사용해서 같은 클래스여도 조회를 해보자. 'getBeansOfType()'을 사용하기 위해서는**

**타입은 'Map'을 사용해야 한다.**

```java
	@Test
	@DisplayName("특정 타입을 모두 조회하기")
	void findAllBeanByType() {
		Map<String, MemberRepository> beansOfType = ac.getBeansOfType(MemberRepository.class);
		// 'getBeansOfType'을 사용하려면, Map으로 사용해야 한다.
		for(String key : beansOfType.keySet()) {
			System.out.println("key = " + key + " value = " + beansOfType.get(key));
		}
		System.out.println("beansOfType = " + beansOfType);
		Assertions.assertThat(beansOfType.size()).isEqualTo(2);
		// 빈즈의 갯수는 2개이다로 검증을 진행한다.
	}
```

**결과를 확인하면, 'Map'의 특성대로 'Key : Value'로 값을 조회하는 것을 확인할 수 있다.**

```java
key = memberRepository1 value = hello.core.member.MemoryMemberRepository@2dbe250d
key = memberRepository2 value = hello.core.member.MemoryMemberRepository@553f1d75
beansOfType = {memberRepository1=hello.core.member.MemoryMemberRepository@2dbe250d, memberRepository2=hello.core.member.MemoryMemberRepository@553f1d75}
```

**결과를 확인하면 모두 다 성공이 된다!**

![](/assets/img/posts/79/2.png)

> **한 번에 조회하는 방법 (= 특정 타입을 모두 조회)을 설명하는 이유**  
> **나중에 'AutoWired'를 사용할 때도 오늘 배운 내용이 다 적용이 된다.  
> 'AutoWired'가 어떻게 돌아가는지 생각해볼 때 도움이 될 수 있다.**
