---
title: "[Spring] Spring Framework - 핵심 원리 (9)- 관심사의 분리"
date: 2022-01-14 23:08:37 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["di 주입", "dip", "객체지향", "스프링 기초", "스프링 프레임워크"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-9-%EA%B4%80%EC%8B%AC%EC%82%AC%EC%9D%98-%EB%B6%84%EB%A6%AC"
---
### **관심사의 분리 + DIP + 객체 지향  + DI**

* * *

#### **관심사의 분리**

-   **애플리케이션을 하나의 공연이라 생각해 보자. 각각의 인터페이스를 배역(배우 역할)이라 생각하자.** 
-   **그런데! 실제 배역 맞는 배우를 선택하는 것은 누가 하는가?**  
    **로미오와 줄리엣 공연을 하면 로미오 역할을 누가 할지 줄리엣 역할을 누가 할지는 배우들이 정 하는 게 아니다.**
-   **이전 코드는 마치 로미오 역할(인터페이스)을 하는 레오나르도 디카 프리오(구현체, 배우)가 줄리엣 역할(인터페이스)을  
    하는 여자 주인공(구현체, 배우)을 직접 초빙하는 것과 같다.**
-   **디카 프리 오는 공연도 해야 하고 동시에 여자 주인공도 공연에 직접 초빙해야 하는 다양한 책임을 가지고 있다.**

```java
public class OrderServiceImpl implements OrderService{
	private final MemberRepository memberRepository = new MemoryMemberRepository();
	//private final DiscountPolicy discountPolicy = new FixDiscountPolicy();
	//private final DiscountPolicy discountPolicy = new RateDiscountPolicy();
```

**관심사를 분리하자**

-   **배우는 본인의 역할 인배 역을 수행하는 것에만 집중해야 한다.**
-   **디카 프리 오는 어떤 여자 주인공이 선택되더라도 똑같이 공연을 할 수 있어야 한다.**
-   **공연을 구성하고, 담당 배우를 섭외하고, 역할에 맞는 배우를 지정하는 책임을 담당하는 별도의 공연 기획자 가나 올 시점이다.**
-   **공연기획자를 만들고, 배우와 공연기획자의 책임을 확실히 분리하자.**

## **그거를 이제 실행해보자!**

* * *

**AppConfig 설정 : 애플리케이션의 전체 동작 방식을 구성(config) 하기 위해, 구현 객체를 생성하고, 연결하는 책임을 가지는 별도의 설정 클래스를 만들자.**

**기존 'MemberServiceImpl' 코드에는 'MemoryMemberRepository'가 들어가 있다.**

**즉, 'MemberServiceImpl' 직접 할당하고 있다.**

```java
public class MemberServiceImpl implements MemberService{
	private final MemberRepository memberRepository = new MemoryMemberRepository();
}
```

**하지만, AppConfig를 사용해서 이 코드를 지우고, '생성자'를 만들자.**

```java
public class MemberServiceImpl implements MemberService{
	private final MemberRepository memberRepository;
	
	public MemberServiceImpl(MemberRepository memberRepository) {
		super();
		this.memberRepository = memberRepository;
	}
```

**생성자를 통해서 'memberRepository'에 '구현체'가 뭐가 들어갈지를 선택한다.**

**이걸 'AppConfig'에서 해주는 것이다.**

**아래 코드는 'MemoryMemberRepository'를 사용한다는 가정이다.**

```java
public class AppConfig {
	// 나의 앱 전체를 설정하고 구성하는 역할을 가진 클래스
	public MemberService memberSevice() {
		return new MemberServiceImpl(new MemoryMemberRepository());
	}
}
```

**이러면 어디선가 'AppConfig'를 통해서 'memberService'를 불러다가 쓸 것이다.**

**그러면, 'memberSevice'의 생성자를 통해서 아래 코드의 'memberRepository'에**

**'new MemoryMemberRepository()'가 들어가게 된다.**

```java
public class MemberServiceImpl implements MemberService{
	private final MemberRepository memberRepository;
	
	public MemberServiceImpl(MemberRepository memberRepository) {
		super();
		this.memberRepository = memberRepository;
	}
```

**여기서 하나 꼭 알아야 할 것은, 'memberServiceImpl' 코드 안에 'MemoryMemberRepository'라는 코드는 전혀 없다. 'MemberRepository'라는 코드만 있다. 'memberServiceImpl'에서는 어떤 구현체가 사용될 것인지는 전혀 모른다. 밖에서 생성해서 넣어주기 때문이다.**

**즉, 생성자를 통해서 주입되어서 '생성자 주입'이라고 한다.**

**이게 바로, '추상화'에만 의존한다는 의미이다.**

**다음은 'orderService'를 만들어보자. 코드는 아래와 같다.**

```java
public class AppConfig {
	// 나의 앱 전체를 설정하고 구성하는 역할을 가진 클래스
	public MemberService memberSevice() {
		return new MemberServiceImpl(new MemoryMemberRepository());
	}
	public OrderService orderService() {
		return new OrderServiceImpl();
	}
}
```

**그리고 'OrderServiceImpl'에 있는 필드는 'memberRepository'와 'discountPolicy' 2개이다.** 

```java
public class OrderServiceImpl implements OrderService{

	private final MemberRepository memberRepository = new MemoryMemberRepository();
	private final DiscountPolicy discountPolicy = new FixDiscountPolicy();
	//private final DiscountPolicy discountPolicy = new RateDiscountPolicy();
}
```

**아래처럼 변경해보자.**

```java
public class OrderServiceImpl implements OrderService{
	private final MemberRepository memberRepository;
	private final DiscountPolicy discountPolicy;

	public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
		super();
		this.memberRepository = memberRepository;
		this.discountPolicy = discountPolicy;
	}
```

**※ 'Final'로 선언이 되어있으면, 기본으로 '할당'을 하던, '생성자'로 할당을 해야 한다.**

**위 코드는 철저하게 'DIP'를 지키고 있는 코드다. 인터페이스에만 의존하고 있다.**

**그리고 다시 'AppConfig'에서 돌아와서 아래처럼 수정해주자.**

**'new MemoryMemberRepository()'와 'new FixDiscountPolicy()'를 쓸 것이다.**

```java
public class AppConfig {
	public MemberService memberSevice() {
		return new MemberServiceImpl(new MemoryMemberRepository());
	}
	
	public OrderService orderService() {
		return new OrderServiceImpl(new MemoryMemberRepository(), new FixDiscountPolicy());
	}
}
```

**AppConfig는 애플리케이션의 실제 동작에 필요한 구현 객체를 생성한다.**  

**AppConfig는 생성한 객체 인스턴스의 참조(레퍼런스)를 생성자를 통해서 주입(연결)해준다.**  

**고친 'MemberServiceImpl' 코드를 다시 봐보자.**

```java
public class MemberServiceImpl implements MemberService{
	private final MemberRepository memberRepository;
	
	public MemberServiceImpl(MemberRepository memberRepository) {
		super();
		this.memberRepository = memberRepository;
	}
	@Override
	public void join(Member member) {
		memberRepository.save(member);
		// '다형성 - Override'에 의해서 'MemoryMemberRepository'안에 있는 'save'가 호출이 된다.
	}
	@Override
	public Member findMember(Long memberid) {
		return memberRepository.findById(memberid);
	}
}
```

-   **설계변경으로 MemberServiceImpl은 MemoryMemberRepository를 의존하지 않는다!**
-   **단지 MemberRepository 인터페이스만 의존한다.**
-   **MemberServiceImpl입장에서 생성자를 통해 어떤 구현 객체가 들어올지(주입될지)는 알 수 없다.**
-   **MemberServiceImpl의 생성자를 통해서 어떤 구현 객체를 주입할지는 오직 외부( AppConfig)에서 결정된다.**
-   **MemberServiceImpl은 이제부터 의존관계에 대한 고민은 외부에 맡기고 실행에만 집중하면 된다.**

**'MemberServiceImpl'는 구현체가 뭐가 될지 신경을 안 쓰고, 인터페이스에 있는 기능만 호출하면 된다!**

![](/assets/img/posts/69/1.png)

*클래스 다이어그램*

-   **객체의 생성과 연결은 AppConfig 가담당한다.**
-   **DIP 완성: MemberServiceImpl은 MemberRepository인 추상에만 의존하면 된다. 이제 구체 클래스를 몰라도 된다.**
-   **관심사의 분리: 객체를 생성하고 연결하는 역할과 실행하는 역할이 명확히 분리되었다.**

![](/assets/img/posts/69/2.png)

-   **appConfig 객체는 memoryMemberRepository 객체를 생성하고 그 참조값을 memberServiceImpl을 생성하면서 생성자로 전달한다.**
-   **클라이언트인 memberServiceImpl 입장에서 보면 의존관계를 마치 외부에서 주입해주는 것 같다고 해서**  
    **DI(Dependency Injection) 우리말로 의존관계 주입 또는 의존성(의존관계) 주입이라 한다.**

**'OrderServiceImpl' 코드도 다시 한번 확인해보자.**

```java
public class OrderServiceImpl implements OrderService{
	private final MemberRepository memberRepository;
	private final DiscountPolicy discountPolicy;

	public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
		super();
		this.memberRepository = memberRepository;
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

-   **설계변경으로 OrderServiceImpl은 FixDiscountPolicy를 의존하지 않는다!** 
-   **단지 DiscountPolicy 인터페이스만 의존한다.**
-   **OrderServiceImpl입장에서 생성자를 통해 어떤 구현 객체가 들어올지(주입될지)는 알 수 없다.** 
-   **OrderServiceImpl의 생성자를 통해서 어떤 구현 객체를 주입할지는 오직 외부( AppConfig)에서 결정한다.**
-   **OrderServiceImpl은 이제부터 실행에만 집중하면 된다.**
-   **OrderServiceImpl에는 MemoryMemberRepository, FixDiscountPolicy 객체의 의존관계가 주입된다.**

**자! 이제 그러면 만들었으니까 실행을 해보자! 'MemberApp' 코드도 다시 수정해보자.**

**기존에는 'MemberApp'에서 'memberService'를 직접 할당했다.**

**그리고 'MemberServiceImpl' 안에서는 'MemoryMemberRepository'를 할당해서 사용했다.**

**순차적으로 진행되었다.**

```java
public class MemberApp {

	public static void main(String[] args) {
		MemberService memberService = new MemberServiceImpl();

		Member member = new Member(1L, "memberA", Grade.VIP);
		memberService.join(member);
		
		Member findMember = memberService.findMember(1L);
		System.out.println("new member = " + member.getName());
		System.out.println("find Member = " + findMember.getName());
	}
}
```

**하지만 이러한 코드를 수정하면 아래와 같다.**

```java
public class MemberApp {

	public static void main(String[] args) {
		AppConfig appConfig = new AppConfig();
		MemberService memberService = appConfig.memberSevice();
		
		Member member = new Member(1L, "memberA", Grade.VIP);
		memberService.join(member);
		
		Member findMember = memberService.findMember(1L);
		System.out.println("new member = " + member.getName());
		System.out.println("find Member = " + findMember.getName());
	}
}
```

**수정된 코드는 'AppConfig'에서 'MemberServiceImpl'을 만들고 'MemoryMemberRepository'을 사용할 것이라고 주입해준다.**

```java
public class AppConfig {
	public MemberService memberSevice() {
		return new MemberServiceImpl(new MemoryMemberRepository()); //'MemoryMemberRepository'객체의 참조값을 'MemberServiceImpl'에 넣어준다.
	}
```

**그리고 'MemberApp'을 실행해보자! 결과는 처음에 진행했던 결과처럼 나온다!**

![](/assets/img/posts/69/3.png)

* * *

**'OrderApp'도 마찬가지이다. 코드부터 확인해보자. 아래처럼 'AppConfig'를 활용해서 수정해보자.**

**이미 'AppConfig'에서 'orderService' 생성자로 'MemoryMemberRepository'와**

**'FixDiscountPolicy'를** **넘겨준다.**

**'OrderServiceImpl'이 'MemoryMemberRepository'와 'FixDiscountPolicy'를 참조하도록 한다.**

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

**그리고 완성된 'OrderServiceImpl' 객체를 'OrderApp'에 반환하는 것이다.**

```java
public class OrderApp {
	public static void main(String[] args) {
		
		AppConfig appConfig = new AppConfig();
		MemberService memberService = appConfig.memberSevice();
		OrderService orderService = appConfig.orderService();

		Long memberId = 1L;
		Member member = new Member(memberId, "memberA", Grade.VIP);
		memberService.join(member);
		
		Order order = orderService.createOrder(memberId, "itemA", 10000);
		System.out.println("order : " + order);
		System.out.println("order.caculatePrice : " + order.calculatePrice());
	}
}
```

**실행해보면 결과는 아래와 같다.**

![](/assets/img/posts/69/4.png)

* * *

### **테스트 코드도 수정해서 확인해보자.**

### **※주석(//)이 기존 코드이다.**

**1\. 'MemberServiceTest' 수정**

```java
public class MemberServiceTest {
		
        //MemberService memberService = new MemberServiceImpl();
	MemberService memberService;
	
	@BeforeEach // 각 테스트가 실행되기전에 실행되는 것
	public void beforeEach() {
		AppConfig appConfig = new AppConfig();
		memberService = appConfig.memberSevice();
		// 즉, 테스트가 돌아가기 전에 'memberService'를 할당하고나서 테스트들이 돌아간다.
	}
```

![](/assets/img/posts/69/5.png)

**2\. 'OrderServiceTest' 수정**

```java
public class OrderServiceTest {
	
	//MemberService memberService = new MemberServiceImpl();
	//OrderService orderService = new OrderServiceImpl();
	MemberService memberService;
	OrderService orderService;
	
	@BeforeEach
	public void BeforeEach() {
		AppConfig appConfig = new AppConfig();
		memberService = appConfig.memberSevice();
		orderService = appConfig.orderService();
	}
```

![](/assets/img/posts/69/6.png)

#### **다 성공적으로 테스트까지 돌아간다.**

**이제야 'DIP'를 지키게 된 것이다.**

**정리**

-   **AppConfig를 통해서 관심사를 확실하게 분리했다. 배역, 배우를 생각해 보자.**
-   **AppConfig는 공연기획자다.** 
-   **AppConfig는 구체 클래스를 선택한다. 배역에 맞는 담당 배우를 선택한다.**
-   **애플리케이션이 어떻게 동작해야 할 지전 체구성을 책임진다.**
-   **이 제각 배우들은 담당 기능을 실행하는 책임만 지면 된다.**
-   **OrderServiceImpl은 기능을 실행하는 책임 만지면 된다.**
