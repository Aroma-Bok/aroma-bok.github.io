---
title: "[Spring] Spring Framework - 핵심 원리 (7)- 주문과 할인 도메인 설계 + 역할과 구현 + 테스트 코드 사용"
date: 2022-01-11 23:35:24 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["long long차이", "테스트 코드", "협력관계"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-6-%EC%A3%BC%EB%AC%B8%EA%B3%BC-%ED%95%A0%EC%9D%B8-%EB%8F%84%EB%A9%94%EC%9D%B8-%EC%84%A4%EA%B3%84-%EC%97%AD%ED%95%A0%EA%B3%BC-%EA%B5%AC%ED%98%84-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%BD%94%EB%93%9C-%EC%82%AC%EC%9A%A9"
---
### **주문과 할인 도메인 설계 + 역할과 구현 + 테스트 코드 사용**

* * *

#### **주문과 할인 정책**

-   **회원 은상 품을 주문할 수 있다.**
-   **회원 등급에 따라 할인 정책을 적용할 수 있다. 할인 정책은 모든 VIP는 1000원을 할인해주는 고정 금액 할인을 적용해달라.**  
    **(나중에 변경될 수 있다.)**
-   **할인 정책은 변경 가능성이 높다. 회사의 기본 할인 정책을 아직 정하지 못했고, 오픈 직전까지 고민을 미루고 싶다.**  
    **최악의 경우 할인을 적용하지 않을 수도 있다. (미확정)**

![](/assets/img/posts/65/1.png)

*주문 도메인 협력, 역할, 책임*

1.  **주문생성: 클라이언트는 주문 서비스에 주문생성을 요청한다.**  
    **\* 여기서 '클라이언트'는 'Main' or 'Controller'가 될 수 있다.**
2.  **회원 조회: 할인을 위해서는 회원 등급이 필요하다. 그래서 주문 서비스는 회원 저장소에서 회원을 조회한다.**
3.  **할인 적용: 주문 서비스는 회원 등급에 따른 할인 여부를 할인 정책에 위임한다.**
4.  **주문 결과 반환: 주문 서비스는 할인 결과를 포함한 주문 결과를 반환한다.**

**역할과 구현을 분리해서 자유롭게 구현 객체를 조립할 수 있게 설계했다. 덕분에 회원 저장소는 물론이고,**  
**할인 정책도 유연하게 변경할 수 있다.**

![](/assets/img/posts/65/2.png)![](/assets/img/posts/65/3.png)

*주문 도메인 전체 / 클래스 다이어그램*

**'MemberRepository', 'DiscountPolicy' 각각의 방법을 생각하면 아래처럼 '객체 다이어그램'이 만들어진다.**

**그리고 'MemberRepository'와 'DiscountPolicy'가 바뀌어도 '주문 서비스 구현체'는 변경하지 않아도 된다.**

**즉, 역할들의 협력 관계를 그대로 재사용할 수 있다.**

![](/assets/img/posts/65/4.png)

*객체 다이어그램1*

![](/assets/img/posts/65/5.png)

*객체 다이어그램2*

* * *

### **주문과 할인 도메인 개발**

#### **1\. 'DiscountPolicy - 인터페이스' 및 'FixDiscountPolicy - 구현체' 생성**

```java
public interface DiscountPolicy {
	/**
	 * @return 할인 대상 금액
	 */
	int discount(Member member, int price);
}
```

```java
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

#### **2\. 'Order - 주문서' 및 'OrderService - 인터페이스', 'OrderServiceImpl - 구현체' 생성**

**'OrderServiceImpl'의 순서는 아래와 같다.**

**1\. 주문 생성 요청이 오면, 회원정보를 먼저 조회한다.**  
**2\. 할인 정책에다가 회원을 넘긴다. (grade만 넘길지, Member 자체를 넘길지는 프로젝트 상황에 맞게 정하면 된다.)**

```java
public class Order {
	private Long memberId;
	private String itemName;
	private int itemPrice;
	private int discountPrice;
	
	public int calculatePrice() { // 계산 기능 추가
		return itemPrice - discountPrice;
	}
    ... 생성자 및 Getter & Setter 생략
}
```

```java
public interface OrderService {
	Order createOrder(Long memberId, String itemName, int itemPrice);
}
```

```java
public class OrderServiceImpl implements OrderService{

	private final MemberRepository memberRepository = new MemoryMemberRepository();
	private final DiscountPolicy discountPolicy = new FixDiscountPolicy();

	@Override
	public Order createOrder(Long memberId, String itemName, int itemPrice) {
		Member member = memberRepository.findById(memberId); // 저장소에서 멤버 찾기
		int discountPrice = discountPolicy.discount(member, itemPrice);
		
		return new Order(memberId, itemName, itemPrice, discountPrice); // 최종 생성된 주문 반환
	}
}
```

**toString 쓰는 이유 : 보통 System.out.println("order = " + order); 을 호출하게 되면, 객체 그 자체를 호출하게 된다. 하지만 'toString'을 'Overriding'해서 깔끔하고 원하는 정보를 볼 수 있도록 도와준다.**

```javascript
@Override
public String toString() {
	return "Order [
    memberId=" + memberId + ",
    itemName=" + itemName + ", 
    itemPrice=" + itemPrice	+ ", 
    discountPrice=" + discountPrice + "]";
}
```

* * *

### **주문과 할인 도메인 실행과 테스트 - 순수 Java**

#### **1\. 주문과 할인 정책 실행**

```java
public class OrderApp {
	public static void main(String[] args) {
		MemberService memberService = new MemberServiceImpl();
		OrderService orderService = new OrderServiceImpl();
		
		// 멤버 생성
		Long memberId = 1L; // 아이디 지정
		Member member = new Member(memberId, "memberA", Grade.VIP);
		memberService.join(member);
		// 멤버를 저장소에 넣는다.
		
		// 오더 생성
		Order order = orderService.createOrder(memberId, "itemA", 10000);
		System.out.println("order : " + order);
		System.out.println("order.caculatePrice : " + order.calculatePrice());
	}
}
```

![](/assets/img/posts/65/6.png)

*결과*

* * *

### **주문과 할인 도메인 실행과 테스트 - JUnit**

#### **1\. 주문과 할인 정책 실행**

**Long & long 차이 : Long을 쓰면 'null'값이 DB에 들어갈 수 있지만, long을 사용하면 DB 'null'값이 들어갈 수 없게 된다.**

**이게 무슨 말이냐면, Long의 값이 null일 때, long에 Long값을 대입하면 '형 변환'이 되지 않아 에러가 발생한다.**

```java
public class OrderServiceTest {
	
	MemberService memberService = new MemberServiceImpl();
	OrderService orderService = new OrderServiceImpl();
	
	@Test
	void createOrder() {
		// given
		Long memberId = 1L;
		Member member = new Member(memberId, "memberA", Grade.VIP);
		
		// when
		memberService.join(member);
		Order order = orderService.createOrder(memberId, "itemA", 10000);
		
		// then
		Assertions.assertThat(order.getDiscountPrice()).isEqualTo(1000);
		// 할인금액이 의도한대로 나오는지 확인한다.
	}
}
```

**테스트를 연습하자!!!**
