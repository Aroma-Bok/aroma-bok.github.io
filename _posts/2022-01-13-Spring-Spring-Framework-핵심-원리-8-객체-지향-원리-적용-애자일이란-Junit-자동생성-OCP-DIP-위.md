---
title: "[Spring] Spring Framework - 핵심 원리 (8)- 객체 지향 원리 적용  + 애자일이란? +  Junit 자동생성 + OCP, DIP 위반 + 객체 주입"
date: 2022-01-13 23:07:43 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["dip", "junit", "ocp", "스프링", "애자일", "프레임워크"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-8-%EA%B0%9D%EC%B2%B4-%EC%A7%80%ED%96%A5-%EC%9B%90%EB%A6%AC-%EC%A0%81%EC%9A%A9-%EC%95%A0%EC%9E%90%EC%9D%BC%EC%9D%B4%EB%9E%80-Junit-%EC%9E%90%EB%8F%99%EC%83%9D%EC%84%B1-OCP-DIP-%EC%9C%84%EB%B0%98-%EA%B0%9D%EC%B2%B4-%EC%A3%BC%EC%9E%85"
---
### **객체 지향 원리 적용  + 애자일이란? +  Junit 자동생성 + OCP, DIP 위반 + 객체 주입**

* * *

**이번에 정리하는 내용은 기획자가 새로운 '할인 정책'을 추가하려고 하는 과정이다.**

**여러 과정을 거치면서, '스프링'의 원리를 파악해보자!**

**새로운 할인 정책을 확장해 보자.**

더보기

**악덕 기획자: 서비스 오픈 직전에 할인 정책을 지금처럼 고정 금액 할인이 아니라 좀 더 합리적인 주문 금액당 할인하는 정률% 할인으로 변경하고 싶어요. 예를 들어서 기존 정책은 VIP가 10000원을 주문하 든 20000원을 주문하 든 항상 1000원을 할인했는데, 이번에 새로 나온 정책은 10% 로지 정해두면 고객이 10000원 주문 시 1000원을 할인해주고, 20000원 주문 시에 2000원을 할인해주는 거예요!**

  
**순진 개발자: 제가 처음부터 고정 금액 할인은 아니라고 했잖아요.**

  
**악덕 기획자: 애자일 소프트웨어 개발 선언 몰라요? “계획을 따르기보다 변화에 대응하기를”** 

**순진 개발자: … (하지만 난 유연한 설계가 가능하도록 객체지향 설계 원칙을 준수했지.. 후후)**

**순진 개발자가 정말 객체지향 설계 원칙을 잘 준수했는지 확인해 보자.**

**이번에는 주문한 금액의 %를 할인해주는 새로운 정률 할인 정책을 추가하자.**

**하지만, 이미 역할과 구분을 구분해서 미리 만들었기 때문에, 걱정할 게 없다!**

* * *

**참고 - 애자일이란?**

더보기

# **애자일 소프트웨어 개발 선언**

**우리는 소프트웨어를 개발하고, 또 다른 사람의 개발을**  
**도와주면서 소프트웨어 개발의 더 나은 방법들을 찾아가고**  
**있다. 이 작업을 통해 우리는 다음을 가치 있게 여기게 되었다:**

**공정과 도구보다 개인과 상호작용을**  
**포괄적인 문서보다 작동하는 소프트웨어를**  
**계약 협상보다 고객과의 협력을**  
**계획을 따르기보다 변화에 대응하기를 가치 있게 여긴다.**

**이 말은, 왼쪽에 있는 것들도 가치가 있지만,**  
**우리는 오른쪽에 있는 것들에 더 높은 가치를 둔다는 것이다.**

* * *

**현재 사용하고 있는 'FixDiscountPolicy'말고 'RateDiscountPolicy'를 하나 만들어서 사용하기만 하면 된다!!**

![](/assets/img/posts/66/1.png)

**'Ctrl + 1'을 누르면 'Junit 테스트 코드'를 자동 생성할 수 있다.**

```java
class RateDiscountPolicyTest {

	RateDiscountPolicy discountPolicy = new RateDiscountPolicy();
	
	@Test
	@DisplayName("VIP는 10% 할인이 적용되어야 한다.")
	void test() {
		//given
		Member member = new Member(1L, "memberVIP", Grade.VIP);
		
		//when
		int discount = discountPolicy.discount(member, 10000);
		
		//then
		Assertions.assertThat(discount).isEqualTo(1000);
	}
}
```

![](/assets/img/posts/66/2.png)

*결과*

**성공도 중요하지만, 테스트를 할 때는, 꼭! 실패 테스트도 만들어봐야 한다.** 

```java
	@Test
	@DisplayName("VIP가 아니면 할인이 적용되지 않아야 한다.")
	void test_vipx() {
		//given
		Member member = new Member(1L, "memberBASIC", Grade.BASIC);
		
		//when
		int discount = discountPolicy.discount(member, 10000);
		
		//then
		Assertions.assertThat(discount).isEqualTo(1000);
	}
```

![](/assets/img/posts/66/3.png)

*결과 / 우측 결과에 너무 잘 나와준다..*

**다시 기대하는 값을 0으로 바꾸면 결과는 합격한다.**

```java
	@Test
	@DisplayName("VIP가 아니면 할인이 적용되지 않아야 한다.")
	void test_vipx() {
		//given
		Member member = new Member(1L, "memberBASIC", Grade.BASIC);
		
		//when
		int discount = discountPolicy.discount(member, 10000);
		
		//then
		Assertions.assertThat(discount).isEqualTo(0);
	}
```

![](/assets/img/posts/66/4.png)

*결과*

**할인 정책을 변경하려면 클라이언트인 'OrderServiceImpl' 코드를 고쳐야 한다.**

**기존 'OrderServiceImpl' 코드에서 'RateDiscountPolicy'를 새롭게 구현체로 가지고 온다.**

```java
public class OrderServiceImpl implements OrderService{

	private final MemberRepository memberRepository = new MemoryMemberRepository();
	//private final DiscountPolicy discountPolicy = new FixDiscountPolicy();
    private final DiscountPolicy discountPolicy = new RateDiscountPolicy(); // 추가

	// 1. 주문 생성 요청이 오면, 회원정보를 먼저 조회한다.
	// 2. 할인정책에다가 회원을 넘긴다. (grade만 넘길지, Member 자체를 넘길지는 프로젝트 상황에 맞게 정하면된다.)
	@Override
	public Order createOrder(Long memberId, String itemName, int itemPrice) {
		Member member = memberRepository.findById(memberId); // 저장소에서 멤버 찾기
		int discountPrice = discountPolicy.discount(member, itemPrice);
		
		return new Order(memberId, itemName, itemPrice, discountPrice); // 최종 생성된 주문 반환
	}
}
```

**하지만 이렇게 진행하면 문제점이 발생한다. 어떤 문제점이 있는지 생각해보자.**

-   **우리는 역할과 구현을 충실하게 분리했다. - OK**
-   **다형 성도 활용하고, 인터페이스와 구현 객체를 분리했다. - OK**
-   **OCP, DIP 같은 객체지향 설계 원칙을 충실히 준수했다. - 그렇게 보이지만 사실은 아니다.**  
    **DIP: 클라이언트( OrderServiceImpl)는 DiscountPolicy 인터페이스에 의존하면서 DIP를 지킨 것 같은데?**  
    **하지만, 추상(인터페이스) 뿐만 아니라 구체(구현) 클래스에도 의존하고 있다.**  
    **추상(인터페이스) 의존: DiscountPolicy**  
    **구체(구현) 클래스: FixDiscountPolicy, RateDiscountPolicy** 
-   **OCP를 변경하지 않고 확장할 수 있다고 했는데!**  
    **지금 코드는 기능을 확장해서 변경하면, 클라이언트 코드에 영향을 준다! 따라서 OCP를 위반한다.**

![](/assets/img/posts/66/5.png)![](/assets/img/posts/66/6.png)

*기대했던 의존관계 실제 의존관계*

![](/assets/img/posts/66/7.png)

**위 다이어그램을 보면, 실제 코드를 보면 알 수 있듯이, 'DIP 위반'을 하고 있다.**

![](/assets/img/posts/66/8.png)

#### **중요!**

**'FixDiscountPolicy'를 'RateDiscountPolicy'로 변경하려면, 'OrderServiceImpl'의 소스코드도 함께**

**변경해야 한다. 즉, 'OCP 위반'이다.**

**그렇다면 어떻게 문제를 해결할 수 있을까?**

-   **클라이언트 코드인 OrderServiceImpl은 DiscountPolicy의 인터페이스뿐만 아니라 구체 클래스도 함께 의존한다.**
-   **그래서 구체 클래스를 변경할 때 클라이언트 코드도 함께 변경해야 한다.** 
-   **DIP 위반 -> 추상에만 의존하도록 변경(인터페이스에만 의존)**  
    **DIP를 위반하지 않도록 인터페이스에만 의존하도록 의존관계를 변경하면 된다.**

**즉, 인터페이스에만 의존하도록 설계하면 된다.**

**'private DiscountPolicy discountPolicy; ' 만 작성하여 추상화인 '인터페이스'에만 의존한다.**

```java
public class OrderServiceImpl implements OrderService{

	private final MemberRepository memberRepository = new MemoryMemberRepository();
	private DiscountPolicy discountPolicy; // 추상화인 '인터페이스'에만 의존한다.

	// 1. 주문 생성 요청이 오면, 회원정보를 먼저 조회한다.
	// 2. 할인정책에다가 회원을 넘긴다. (grade만 넘길지, Member 자체를 넘길지는 프로젝트 상황에 맞게 정하면된다.)
	@Override
	public Order createOrder(Long memberId, String itemName, int itemPrice) {
		Member member = memberRepository.findById(memberId); // 저장소에서 멤버 찾기
		int discountPrice = discountPolicy.discount(member, itemPrice);
		
		return new Order(memberId, itemName, itemPrice, discountPrice); // 최종 생성된 주문 반환
	}
}
```

**하지만, 결과는 어떨까? 테스트 코드로 확인해보자.**

```java
public class OrderServiceTest {
	
	MemberService memberService = new MemberServiceImpl();
	OrderService orderService = new OrderServiceImpl();
	@Test
	void createOrder() {
		// given
		Long memberId = 1L;
		Member member = new Member(memberId, "memberA", Grade.VIP);
		// Long대신 long을 안쓰는 이유 : Long을 쓰면 'null'값이 DB에 들어갈 수 있지만, long그렇지 못하다.
		
		// when
		memberService.join(member);
		Order order = orderService.createOrder(memberId, "itemA", 10000);
		
		// then
		Assertions.assertThat(order.getDiscountPrice()).isEqualTo(1000);
		// 할인금액이 의도한대로 나오는지 확인한다.
	}
}
```

![](/assets/img/posts/66/9.png)

**결과는 'NullPointException'이 발생한다. 왜일까?**

**위 'OrderServiceImpl' 코드에서 'discountPrice'는 아무것도 할당이 안되어있다.**

**그러니까 당연히 에러가 날 수밖에 없다.**

```java
int discountPrice = discountPolicy.discount(member, itemPrice);
```

**그럼 정답이 뭘까..?**

**바로, 누군가가 클라이언트인 'OrderServiceImpl'에 'DiscountPolicy'의 구현 객체를** **대신**

**주입을 해주어야 한다.**

### **그 누군가가 바로 'Spring'이다.**
