---
title: "[Spring] Spring Framework - 핵심 원리 (36) - 조회 빈이 2개 이상"
date: 2022-03-16 23:49:19 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["nouniquebeandefinitionexception", "스프링빈이 2개 이상일 때"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-36-%EC%A1%B0%ED%9A%8C-%EB%B9%88%EC%9D%B4-2%EA%B0%9C-%EC%9D%B4%EC%83%81"
---
### **조회 빈이 2개 이상일 때 발생되는 문제 + NoUniqueBeanDefinitionException**

* * *

**@Autowired는 타입(Type)으로 조회한다.**

```java
     @Autowired 
     public OrderServiceImpl(MemberRepository memberRepository,DiscountPolicy discountPolicy) {
		this.memberRepository =  memberRepository; 
		this.discountPolicy = discountPolicy; 
 }
```

**타입으로 조회하기 때문에, 마치 다음 코드와 유사하게 동작한다.**

```java
ac.getBean(DiscountPolicy.class)
```

**스프링 빈 조회에서 학습했듯이 타입으로 조회하면 선택된 빈이 2개 이상일 때 문제가 발생한다.**

**DiscountPolicy의 하위 타입인 FixDiscounPolicy, RateDiscountPolicy 둘 다 스프링 빈으로 선언해보자.**

```java
@Component
public class RateDiscountPolicy implements DiscountPolicy{

	private int discountPercent = 10;
	
	@Override
	public int discount(Member member, int price) {
		if(member.getGrade() == Grade.VIP) {
			return price * discountPercent / 100; // 로직을 의심해야한다. 즉, 테스트 해봐야 한다는 말!
		}else {
			return 0;
		}
	}
}
```

```java
@Component
public class FixDiscountPolicy implements DiscountPolicy{

	private int discountFixAmount = 1000; //1000원 할인
	
	@Override
	public int discount(Member member, int price) {
		if (member.getGrade() == Grade.VIP) { // 'ENUM'은 '=='을 사용해서 확인한다.
			return discountFixAmount;
		}else {
			return 0;
		}
	}
}
```

**이렇게 의존관계를 자동 주입하고 AutoAppConfigTest를 실행하면 결과가 아래처럼 나온다.**

![](/assets/img/posts/111/1.png)

```java
expected single matching bean but found 2
```

**오류 메시지가 친절하게도 하나의 빈을 기대했는데, 2개의 빈이 발견되었다고 알려준다.**

**이때 하위 타입으로 지정할 수도 있지만, 하위 타입으로 지정하는 것은 DIP를 위배하고 유연성이 떨어진다.**

**그리고 이름만 다르고, 완전히 똑같은 타입의 스프링 빈이 2개 있을 때 해결이 안 된다.**

**스프링 빈을 수동 등록해서 문제를 해결돼 해도 되지만, 의존관계 자동 주입에서 해결하는 여러 방법이 있다.**

```java
     @Autowired 
     public OrderServiceImpl(MemberRepository memberRepository,DiscountPolicy discountPolicy) {
		this.memberRepository =  memberRepository; 
		this.discountPolicy = discountPolicy; 
 }
```

> **NoUniqueBeanDefinitionException는 자주 보게 될 에러이다. 잘 알아두자.**  
> **: 필요한 빈이 하나이어야 하는데 하나 이상이 발견되었을 때, 발생하는 에러.**
