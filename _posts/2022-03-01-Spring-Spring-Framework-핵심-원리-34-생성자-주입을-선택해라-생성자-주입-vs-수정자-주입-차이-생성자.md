---
title: "[Spring] Spring Framework - 핵심 원리 (34) - 생성자 주입을 선택해라 + 생성자 주입 vs 수정자 주입 차이 + 생성자 주입의 장점 + final 키워드"
date: 2022-03-01 00:28:24 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["final 키워드", "생성자 주입", "생성자 주입 vs 수정자 주입", "생성자 주입 특징", "수정자 주입", "필드 주입"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-34-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%A3%BC%EC%9E%85%EC%9D%84-%EC%84%A0%ED%83%9D%ED%95%B4%EB%9D%BC-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%A3%BC%EC%9E%85-vs-%EC%88%98%EC%A0%95%EC%9E%90-%EC%A3%BC%EC%9E%85-%EC%B0%A8%EC%9D%B4-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%A3%BC%EC%9E%85%EC%9D%98-%EC%9E%A5%EC%A0%90-final-%ED%82%A4%EC%9B%8C%EB%93%9C"
---
### **생성자 주입을 선택해라 + 생성자 주입 vs 수정자 주입 차이 + 생성자 주입의 장점 + final 키워드**

* * *

#### **생성자 주입을 선택해라!**

**과거에는 수정자 주입과 필드 주입을 많이 사용했지만, 최근에는 스프링을 포함한 DI 프레임워크 대부분이  
생성자 주입을 권장한다, 그 이유는 다음과 같다.**

#### **불변**

-   **대부분의 의존관계 주입은 한번 일어나면 애플리케이션 종료 시점까지 의존관계를 변경할 일이 없다.**  
    **오히려 대부분의 의존관계는 애플리케이션 종료 전까지 변하면 안 된다. (불변해야 한다.)**
-   **수정자 주입을 사용하면, setXxx 메서드를 public으로 열어두어야 한다.**
-   **누군가 실수로 변경할 수도 있고, 변경하면 안 되는 메서드를 열어두는 것은 좋은 설계 방법이 아니다.**
-   **생성자 주입은 객체를 생성할 딱 1번만 호출되므로 이후에 호출되는 일이 없다. 따라서 불변하게 설계할 수 있다.**

#### **누락**

**프레임워크 없이 순수한 자바 코드를 단위 테스트하는 경우에 다음과 같이 수정자 의존 관계인 경우**

#### **1\. OrderServiceImpl 수정자 의존관계 작성**

```java
@Component
public class OrderServiceImpl implements OrderService{

	 private  MemberRepository memberRepository;
     private  DiscountPolicy discountPolicy;
	
     @Autowired
     public void setMemberRepository(MemberRepository memberRepository) {
		this.memberRepository = memberRepository;
	}
     @Autowired
     public void setDiscountPolicy(DiscountPolicy discountPolicy) {
		this.discountPolicy = discountPolicy;
	}
    
    	@Override
	public Order createOrder(Long memberId, String itemName, int itemPrice) {
		Member member = memberRepository.findById(memberId); // 저장소에서 멤버 찾기
		int discountPrice = discountPolicy.discount(member, itemPrice);
		
		return new Order(memberId, itemName, itemPrice, discountPrice); // 최종 생성된 주문 반환
	}
  }
```

#### **2\. 테스트 코드 작성**

```java
public class OrderServiceImplTest {
	
	@Test
	void createOrder() {
		OrderServiceImpl orderServiceImpl = new OrderServiceImpl();
		orderServiceImpl.createOrder(1L, "item1", 10000);
	}
}
```

**결과는 NullPointException이 발생한다.  
**

**왜일까? 아무리 내가 createOrder만 테스트를 한다고 해도, 코드를 확인해보면, memberRepository와 discountPolicy가 필요하다. 즉, 누락이 된 것이다.**

**단순히 테스트 코드를 짜는 입장에서는 의존관계를 한 번에 확인할 수 없다.**

![](/assets/img/posts/105/1.png)

**하지만, 이것을 생성자 주입으로 사용하면 어떨까?**

```java
@Component
public class OrderServiceImpl implements OrderService{

	 private  MemberRepository memberRepository;
     private  DiscountPolicy discountPolicy; 
		
	@Autowired 
    public OrderServiceImpl(MemberRepository memberRepository,DiscountPolicy discountPolicy) {
		this.memberRepository = memberRepository;
		this.discountPolicy = discountPolicy; 
	}
		  
	@Override
	public Order createOrder(Long memberId, String itemName, int itemPrice) {
		Member member = memberRepository.findById(memberId); 
		int discountPrice = discountPolicy.discount(member, itemPrice);
		
		return new Order(memberId, itemName, itemPrice, discountPrice); 
	}
```

**생성자 주입으로 바꾸고 테스트를 작성을 하면, 아래처럼 바로 무엇이 잘못되었는지 인지가 가능하다!**

**그래서 임의로 넣어서 테스트를 진행할 수 있다. (누락 방지!)**

![](/assets/img/posts/105/2.png)

**제대로 된 테스트는 아래처럼 작성할 수 있다.**

```java
public class OrderServiceImplTest {
	
	@Test
	void createOrder() {
		MemoryMemberRepository memberRepository = new MemoryMemberRepository();
		memberRepository.save(new Member(1L,"name",Grade.VIP)); // 임의로 회원생성
		
		OrderServiceImpl orderServiceImpl = new OrderServiceImpl(memberRepository, new FixDiscountPolicy());
		Order order = orderServiceImpl.createOrder(1L, "item1", 10000);
		Assertions.assertThat(order.getDiscountPrice()).isEqualTo(1000);
	}
}
```

**생성자 주입을 선택해야 스프링 없이 순수한 JAVA 코드를 이용해서 테스트를 만들 수 있다.**

**그리고 파이널(final) 키워드를 사용할 수 있다는 것이다.**

**final은 초기애 값을 넣어주던가, 생성자를 통해서만 값을 넣어줄 수 있다.**

**그래서 생성자에서 혹시라도 값이 설정되지 않는 오류를 컴파일 시점에 막아준다.**

**아래 사진을 보고 비교해보자**

#### **1\. final 키워드 사용 X -  생성자에 값을 누락해도 컴파일 오류가 발생하지 않음.**

![](/assets/img/posts/105/3.png)

#### **2\. final 키워드 사용 O -  생성자에 값을 누락하면 컴파일 오류 발생.  
(java : variable discountPolicy might not have been initialize)**

![](/assets/img/posts/105/4.png)

**기억하자!! 컴파일 오류는 세상에서 가장 빠르고, 좋은 오류다!**

> **참고 : 수정자 주입을 포함한 나머지 주입 방식은 모두 생성자 이후에 호출되므로, 필드에 final 키워드를 사용할 수 없다. 오직 생성자 주입 방식만 final 키워드를 사용할 수 있다.**

* * *

​

#### **정리**

1.  **생성자 주입 방식을 선택하는 이유는 여러 가지가 있지만, 프레임 워크에 의존하지 않고, 순수한 자바 언어의 특징을 잘 살리는 방법이기도 하다.**
2.  **기본으로 생성자 주입을 사용하고, 필수 값이 아닌 경우에는 수정자 주입 방식을 옵션으로 부여하면 된다.**  
    **생성자 주입과 수정자 주입을 동시에 사용할 수 있다.**
3.  **항상 생성자 주입을 선택해라! 그리고 가끔 옵션이 필요하면 수정자 주입을 선택해라. 필드 주입은 사용하지 않는 게 좋다!**
