---
title: "[Spring] Spring Framework - 핵심 원리 (10) - AppConfig 리팩터링 + OCP, DIP 만족하는 코드 + 리팩터링 방법 + 메서드 추출 단축키(Alt + Shift + M)"
date: 2022-01-17 23:34:17 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["ocp dip 만족", "리팩터링", "리팩터링 방법", "메서드 추출 단축키"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-10-AppConfig-%EB%A6%AC%ED%8C%A9%ED%84%B0%EB%A7%81-OCP-DIP-%EB%A7%8C%EC%A1%B1%ED%95%98%EB%8A%94-%EC%BD%94%EB%93%9C-%EB%A6%AC%ED%8C%A9%ED%84%B0%EB%A7%81-%EB%B0%A9%EB%B2%95-%EB%A9%94%EC%84%9C%EB%93%9C-%EC%B6%94%EC%B6%9C-%EB%8B%A8%EC%B6%95%ED%82%A4Alt-Shift-M"
---
### **AppConfig 리팩터링 + OCP, DIP 만족하는 코드 + 리팩터링 방법 + 메서드 추출 단축키(Alt + Shift + M)**

* * *

**현재의 AppConfig를 보면 중복도 좀 있고, 역할에 따른 구현이 잘 보이지 않는다.**

**즉, 아래의 그림처럼 역할과 구분을 분리해서 한 그림에 보여야 하는데, 현재의 AppConfig는 그렇지 못하다.**

![](/assets/img/posts/70/1.png)

*한 눈에 보이는 역할과 구현의 분리*

**현재의 AppConfig 안에서 'new MemoryMemberRepository()'가 중복되고 있는 것을 볼 수 있다.**

```java
public class AppConfig {
	public MemberService memberSevice() {
		return new MemberServiceImpl(new MemoryMemberRepository()); //'MemoryMemberRepository'객체의 참조값을 'MemberServiceImpl'에 넣어준다.
	}
	public OrderService orderService() {
		return new OrderServiceImpl(new MemoryMemberRepository(), new FixDiscountPolicy());
	}
}
```

**'new MemoryMemberRepository()'에 커서를 갖다 대고 'Alt + Shift + M'을 누르면 추출이 가능하다.**

### **Alt + Shift + M = 특정 메서드 추출**

![](/assets/img/posts/70/2.png)

*Alt + Shift + M*

**이름과 타입을 아래처럼 생성하였다.**

```java
public class AppConfig {
	public MemberService memberSevice() {
		return new MemberServiceImpl(memberRepository())
	}
	private MemberRepository memberRepository() {
		return new MemoryMemberRepository();
	}
	public OrderService orderService() {
		return new OrderServiceImpl(memberRepository(), new FixDiscountPolicy());
	}
}
```

**추가적으로 남은 'DiscountPolicy'를 리팩터링 해주자.**

```java
	public OrderService orderService() {
		return new OrderServiceImpl(memberRepository(), discountPolicy());
	}

	private DiscountPolicy discountPolicy() {
		return new FixDiscountPolicy();
	}
```

**장점** 

-   **메서드명을 보는 순간 역할이 다 드러난다. 나중에 구현체를 바꾼다면, 'return' 값 뒤에 넣어준 값만 변경하면  
    쉽게 변경할 수 있다.**

**정리** 

-   **new MemoryMemberRepository() 이 부분 이중복 제거되었다. 이제 MemoryMemberRepository를   
    다른 구현체로 변경할 때 한 부분만 변경하면 된다.**
-   **AppConfig를 보면 역할과 구현 클래스가 한눈에 들어온다. 애플리케이션 전체 구성이 어떻게 되어있는지  
    빠르게 파악할 수 있다.**

* * *

**위에서 배운 방법을 이용해서 새로운 구조와 할인 정책을 적용해보도록 하자.**

**이제는 'AppConfig'만 변경하면 된다.  'AppConfig'를 통해서 '사용 영역'과 '구성 영역'으로 나눌 수 있다.**

![](/assets/img/posts/70/3.png)![](/assets/img/posts/70/4.png)

**자 이제 코드로 진행해보자. 아래처럼 'FixDiscountPolicy'를 'RateDiscountPolicy'로 변경만 해주면 된다.**

**그리고 'OrderApp'을 실행시켜서 결과를 확인해보자.** 

**추가로, 'FixDiscountPolicy'로 변경해서 결과를 돌려보자. 아래처럼 결과가 잘 나오는 것을 확인할 수 있다.**

```java
	private DiscountPolicy discountPolicy() {
		//return new FixDiscountPolicy();
		return new RateDiscountPolicy();
	}
```

![](/assets/img/posts/70/5.png)

*RateDiscountPolicy*

![](/assets/img/posts/70/6.png)

*FixDiscountPolicy*

**즉, 구현체를 변경해도 구성 영역(AppConfig)만 영향을 받고, 사용 영역은 전혀 영향을 받지 않는다!!**

**여기서 가장 중요한 것은, '사용 영역'에 있는 것은 전혀 바꾸지 않아도 된다!!**

**클라이언트 코드인 'OrderServiceImpl'을 확인해보면 'OCP'와 'DIP'가 지켜진 것이 보인다.**

```java
public class OrderServiceImpl implements OrderService{
	private final MemberRepository memberRepository;
	private final DiscountPolicy discountPolicy; // 추상화인 '인터페이스'에만 의존한다.
```

**꼭 코드를 보면서 다시 한번 전에 배웠던 것을 정리해보자!**
