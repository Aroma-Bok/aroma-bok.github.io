---
title: "[Spring] Spring Framework - 핵심 원리 (37) - @Autowired, @Qualifier, @Primary + 조회 대상 빈이 2개 이상일 때, 해결방법 + @Qualifier VS @Primary 차이점 + @Qualifier VS @Primary 우선순위"
date: 2022-03-17 23:35:31 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["@autowired", "@primary", "@primary vs @qualifier", "@primary 와 @qualifier 우선순위", "@qualifier", "@qualifier vs @autowired"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-37-Autowired-Qualifier-Primary-%EC%A1%B0%ED%9A%8C-%EB%8C%80%EC%83%81-%EB%B9%88%EC%9D%B4-2%EA%B0%9C-%EC%9D%B4%EC%83%81%EC%9D%BC-%EB%95%8C-%ED%95%B4%EA%B2%B0%EB%B0%A9%EB%B2%95-Qualifier-VS-Primary-%EC%B0%A8%EC%9D%B4%EC%A0%90-Qualifier-VS-Primary-%EC%9A%B0%EC%84%A0%EC%88%9C%EC%9C%84"
---
### **@Autowired, @Qualifier, @Primary + 조회 대상 빈이 2개 이상일 때, 해결방법 + @Qualifier VS @Primary 차이점 + @Qualifier VS @Primary 우선순위**

* * *

**앞서 배웠던 내용에서, 같은 타입(Type)의 빈이 있다면 오류가 발생하는 경우가 생긴다.**

**어떻게 해결해야 하는지 하나씩 알아보자.**

#### **조회 대상 빈이 2개 이상일 때, 해결방법**

**1\. @Autowired 필드 명 매칭**

**2\. @Qualifier -> @Qualifier끼리 매칭 -> 빈 이름 매칭**

**3\. @Primary 사용**

#### **1. @Autowired 필드 명 매칭 (타입 -> 필드 -> 파라미터)**

**@Autowired는 타입 매칭을 시도하고, 이때 여러 빈이 있으면, 필드 이름, 파라미터 이름으로 빈 이름을 추가 매칭 한다.**

**무슨 말이냐면, 기존 코드를 보자.**

```java
     @Autowired 
     public OrderServiceImpl(MemberRepository memberRepository,DiscountPolicy discountPolicy) {
		this.memberRepository =  memberRepository; 
		this.discountPolicy = discountPolicy; 
 }
```

**여기서 파라미터로 들어가는 'discountPolicy'를 'rateDiscountPolicy'로 변경해보자.**

```java
     @Autowired 
     public OrderServiceImpl(MemberRepository memberRepository,DiscountPolicy rateDiscountPolicy) {
		this.memberRepository =  memberRepository; 
		this.discountPolicy = rateDiscountPolicy; 
 }
```

**그리고 테스트를 돌려보면 테스트가 성공한다!**

![](/assets/img/posts/112/1.png)

![](/assets/img/posts/112/2.png)

**필드명이 'rateDiscountPolicy' 이므로 정상 주입된다.**

**필드명 매칭은 먼저 타입 매칭을 시도하고 그 결과에 여러 빈이 있을 때 추가로 동작하는 기능이다.**

**@Autowired 매칭 정리**

**1\. 타입 매칭**

**2\. 타입 매칭의 결과가 2개 이상일 때, 필드 명, 파라미터 명으로 빈 이름 매칭**

* * *

#### **2\. @Qualifier 사용**

**@Qualifier는 추가 구분자를 붙여주는 방법이다. 주입 시 추가적인 방법을 제공하는 것이지 빈 이름을 변경하는 것은 아니다.**

**기존 코드를 보자.**

```java
@Component
public class RateDiscountPolicy implements DiscountPolicy
```

```java
@Component
public class FixDiscountPolicy implements DiscountPolicy
```

**위 각각의 빈에 @Qulifier를 지정해서 사용해보자.**

```java
@Component
@Qualifier("rateDiscountPolicy")
public class RateDiscountPolicy implements DiscountPolicy
```

```java
@Component
@Qualifier("mainDiscountPolicy")
public class FixDiscountPolicy implements DiscountPolicy
```

**그리고 테스트 코드에 @Qualifier를 사용해서 아래처럼 수정하고 테스트를 실행해보자.**

```java
     @Autowired 
     public OrderServiceImpl(MemberRepository memberRepository, @Qualifier("mainDiscountPolicy")DiscountPolicy discountPolicy) {
		this.memberRepository =  memberRepository; 
		this.discountPolicy = discountPolicy; 
 }
```

![](/assets/img/posts/112/3.png)

![](/assets/img/posts/112/4.png)

**결과를 확인해보면, '@Qualifier("mainDiscountPolicy")'로 지정해둔 FixDiscountPolicy를 빈으로**

**사용하는 것을 볼 수 있다.**

> **여기서 잠깐!**  
> **@Qualifier로 주입할 때, @Qualifier("mainDiscountPolicy")를 못 찾을 수도 있다. 그러면 어떻게 될까?**  
> **그러면 mainDiscountPolicy라는 이름의 스프링 빈을 추가로 찾는다. 하지만, @Qualifier는 @Qualifier를 찾는  
> 용도로만 사용하는 게 명확하고 좋다고 한다.**

**@Qualifier 정리**

**1\. @Qualifier끼리 매칭**

**2\. 못 찾으면, 빈 이름 매칭**

**3\. 그래도 못 찾으면, NoSuchBeanDefinitionException 예외 발생**

* * *

#### **3\. @Primary 사용** 

**@Primary는 우선순위를 정하는 방법이다. @Autowired 시에 여러 빈이 매칭 되면, @Primary가 우선권을**

**가진다.**

```java
@Component
public class RateDiscountPolicy implements DiscountPolicy
```

```java
@Component
public class FixDiscountPolicy implements DiscountPolicy
```

**다시 기존 코드로 돌려놓고 'RateDiscountPolicy'에 @Primary를 적용해보자.**

**(+ 테스트 코드도 원상 복귀시키자.)**

```java
@Component
@Primary
public class RateDiscountPolicy implements DiscountPolicy
```

```java
@Component
public class FixDiscountPolicy implements DiscountPolicy
```

**결과를 확인해보면 테스트는 성공한다!** 

![](/assets/img/posts/112/5.png)

![](/assets/img/posts/112/6.png)

**코드를 실행해보면 문제없이 @Primary가 잘 동작하는 것을 확인할 수 있다.**

**여기까지 보면 @Primary와 @Qualifier 중에 어떤 것을 사용하면 좋을지 고민이 될 것이다.**

**@Qualifier의 단점은 주입받을 때, 다음과 같이 모든 코드에 @Qualifier를 붙여주어야 한다는 점이다.**

**반면에, @Primary를 사용하면 이렇게 @Qualifier를 붙일 필요가 없다.**

* * *

#### **@Primary, @Qualifier 활용**

**코드에서 자주 사용하는 메인 데이터베이스의 커넥션을 획득하는 스프링 빈이 있고, 코드에서 특별한 기능으로 가끔 사용하는 서브 데이터베이스의 커넥션을 획득하는 스프링 빈이 있다고 생각해보자. 메인 데이터베이스의 커넥션을 획득하는 스프링 빈은 @Primary를 적용해서 조회하는 곳에서 @Qualifier 지정 없이 편리하게 조회하고, 서브 데이터베이스 커넥션 빈을 획득할 때는 @Qualifier를 지정해서 명시적으로 획득하는 방식으로 사용하면 코드를 깔끔하게 유지할 수 있다. 물론 이때 메인 데이터베이스의 스프링 빈을 등록할 때, @Qualifier를 지정해주는 것은 상관없다.**

#### **우선순위**

**@Primary는 기본값처럼 동작하는 것이고, @Qualifier는 매우 상세하게 동작한다. 이런 경우 어떤 것이 우선권을 가져갈까? 스프링은 자동보다는 수동이, 넓은 범위의 선택권보다는 좁은 범위의 선택권이 우선순위가 높다.** **따라서 여기서도 @Qualifier가 우선권이 높다.**
