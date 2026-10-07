---
title: "[Spring] Spring Framework - 핵심 원리 (32)  - 다양한 의존관계 주입 방법"
date: 2022-02-25 00:38:46 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["@autowired 종류", "생성자 주입", "수정자 주입", "의존관계 주입 방법", "일반 메서드 주입", "자바 빈 프로퍼티", "필드 주입"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-33-%EB%8B%A4%EC%96%91%ED%95%9C-%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84-%EC%A3%BC%EC%9E%85-%EB%B0%A9%EB%B2%95"
---
### **다양한 의존관계 주입 방법 (생성자 주입, 수정자 주입(Setter 주입), 필드 주입, 일반 메서드 주입) + 자바 빈 프로퍼티 규칙(규약) + @Autowired 종류**

* * *

**의존관계 주입은 크게 4가지 방법이 있다.**

**1\. 생성자 주입**

**2\. 수정자 주입 (setter 주입)**

**3\. 필드 주입**

**4\. 일반 메서드 주입**

* * *

### **생성자 주입**

**: 이름 그대로 생성자를 통해서 의존 관계를 주입받는 방법이다.**

**지금까지 우리가 진행했던 방 빕이 바로 생성자 주입이다.**

#### **특징**

**생성자 호출 시점에 딱 1번만 호출되는 것이 보장된다.**

**불변, 필수 의존관계에 사용**

**아래 코드를 보면, 생성자에 @Autowired가 붙어있다. @Component에 의해 Bean에 등록이 될 때,**

**생성자가 호출이 된다. 그럼 자동적으로 생성자에 붙은 @Autowired에 의해서 스프링 컨테이너에서 MemberRepository와 DiscountPolicy을 꺼내서 자동으로 주입해준다.**

```java
@Component
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

**여기서 불변, 필수 조건에 대해서 코드를 갖고 설명을 해보면 이와 같다.**

**현재 코드에서는 생성자를 통해서만 memberRepository와 discountPolicy가 의존관계가 주입이 된다.**

**외부에서는 어느 누구도 인스턴스를 수정할 수 없다.**

> **참고**  
> **불변이어야 하는 인스턴스에 대해서 Setter가 있으면 나중에 어떤 일이 벌어질지 모르므로, 아래와 같은 Setter는 만들지 않는 게 좋다. 즉, 불변이어야 하는 것은 가급적이면 생성자에만 값을 넣자!**

```java
	public void setDiscountPolicy(DiscountPolicy discountPolicy) {
		this.discountPolicy = discountPolicy;
	}
    
   // 없어야 하는 코드!!
```

### **중요!!  
****생성자가 딱 1개만 있으면 @Autowired를 생략해도 자동 주입된다.  
물론 스프링 빈에만 해당한다.**

**아래처럼 생성자가 2개인 경우에는 반드시 @Autowired를 붙여야 한다!!**

```java
	@Autowired
	public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
		super();
		this.memberRepository = memberRepository;
		this.discountPolicy = discountPolicy;
	}

	public OrderServiceImpl() {
		this.memberRepository = null;
		this.discountPolicy = null;
	}
```

**1개인 경우에 @Autowired를 생략하고 테스트를 진행해보자.**

```java
	public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
		System.out.println("memberRepository = " + memberRepository);
		System.out.println("discountPolicy = " + discountPolicy);
		this.memberRepository = memberRepository;
		this.discountPolicy = discountPolicy;
	}
```

**결과를 확인해보면 잘 들어오는 것을 확인할 수 있다!**

더보기

더보기

**memberRepository = hello.core.member.MemoryMemberRepository@10650953**  
**discountPolicy = hello.core.discount.RateDiscountPolicy@659 eef7**

**즉, 스프링 빈 인경우에 생성자가 1개인 경우에는 자동으로 의존관계를 주입해준다.**

* * *

### **수정자 주입 (Setter 주입)**

**: Setter라 불리는 필드의 값을 변경하는 수정자 메서드를 통해서 의존관계를 주입하는 방법이다.**

#### **특징**

**선택, 변경 가능성이 있는 의존관계에 사용.**

**자바 빈 프로버티 규약의 수정자 메서드 방식을 사용하는 방법이다.**

> **참고**  
> **자바 빈 프로퍼티 - 자바에서는 과거부터 필드의 값을 직접 변경하지 않고, setXxxm getXxx라는 메서드를 통해서 값을 읽거나 수정하는 규칙을 만들었는데, 그것이 자바 빈 프로퍼티 규약이다.**

**보통 관례상 값을 아래처럼 값을 변경해야 하는 필드가 있다면, Setter를 이용해야 한다.**

**그리고 그 Setter에 @Autowired를 붙여줘서 사용하는 방법이다.**

```java
	private  MemberRepository memberRepository;
	private  DiscountPolicy discountPolicy;
	
	@Autowired
	public void setMemberRepository(MemberRepository memberRepository) {
		System.out.println("memberRepository = " + memberRepository);
		this.memberRepository = memberRepository;
	}
	@Autowired
	public void setDiscountPolicy(DiscountPolicy discountPolicy) {
		System.out.println("discountPolicy = " + discountPolicy);
		this.discountPolicy = discountPolicy;
	}
```

**테스트를 돌려서 확인해보면 아래처럼 잘 들어오는 것을 확인할 수 있다.**

```java
memberRepository = hello.core.member.MemoryMemberRepository@2289aca5
discountPolicy = hello.core.discount.RateDiscountPolicy@184497d1
```

**@Autowired를 빼고 호출하면 당연히 Setter가 호출이 안된다!!**

**스프링 컨테이너는 크게 2가지를 한다. 그리고 발생하는 순서도 아래처럼 발생한다.**

**1\. 스프링 빈 등록**  
**\=> @Component로 인해서 Bean을 등록할 때, 당연히 생성자를 호출하게 되고 생성자에 대해서 자동 주입을 진행한다.**

**2\. 의존관계 자동 주입**

**\=> 그리고 그다음에 발생한다.**

**순서를 확인하기 위해서 테스트를 해보자.**

```java
	@Autowired
	public void setMemberRepository(MemberRepository memberRepository) {
		System.out.println("memberRepository = " + memberRepository);
		this.memberRepository = memberRepository;
	}
	@Autowired
	public void setDiscountPolicy(DiscountPolicy discountPolicy) {
		System.out.println("discountPolicy = " + discountPolicy);
		this.discountPolicy = discountPolicy;
	}

	@Autowired
	public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
		System.out.println("1.OrderServiceImpl");
		this.memberRepository = memberRepository;
		this.discountPolicy = discountPolicy;
	}
```

**콘솔에서 아래처럼 찍히는 것을 확인할 수 있다.**

```java
1.OrderServiceImpl
memberRepository = hello.core.member.MemoryMemberRepository@184497d1
discountPolicy = hello.core.discount.RateDiscountPolicy@6ffab045
```

> **참고**  
> **@Autowired의 기본 동작은 주입할 대상이 없으면 오류가 발생한다. 주입할 대상이 없어도 동작하게 하려면**  
> **@Autowired(required = false)로 지정하면 된다.**

* * *

### **필드 주입**

**: 이름 그대로 필드에 바로 주입하는 방법이다.**

#### **특징**

**코드가 간결해서 많은 개발자들을 유혹하지만 외부에서 변경이 불가능해서 테스트하기 힘들다는 치명적인 단점이 있다. DI 프****레임워크가 없으면 아무것도 할 수 없다.**

#### **그냥 사용하지 말자!!**

**애플리케이션의 실제 코드와 관계없는 테스트 코드에서 그리고 스프링 설정을 목적으로 하는 @Configuation 같은 곳에서만 특별한 용도로 사용하자.**

**코드로 알아보자.  아래처럼 주입하는 게 필드 주입이다.** 

```java
	@Autowired private MemberRepository memberRepository;
	@Autowired private DiscountPolicy discountPolicy
```

**테스트 코드를 조금 수정해서 제대로 돌아가는지 확인해보자.**

```java
	@Test
	public void basicScan() {
		AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AutoAppConfig.class);
		
		MemberService memberService = ac.getBean(MemberService.class);
		Assertions.assertThat(memberService).isInstanceOf(MemberService.class);
		
		OrderServiceImpl bean = ac.getBean(OrderServiceImpl.class);
		MemberRepository memberRepository = bean.getMemberRepository();
		System.out.println("memberRepository = " + memberRepository);
	}
}
```

**아래처럼 잘 나오는 것을 확인할 수 있다.**

```java
memberRepository = hello.core.member.MemoryMemberRepository@64da2a7
```

**간결하고 좋지만 외부에서 변경이 불가능해서 테스트하기가 너무 힘들다. 아래 코드를 보자.** 

**결과를 확인하면 당연히 NullPointException이 발생한다.**

```java
	@Test
	public void fieldInjectionTest() {
		OrderServiceImpl orderServiceImpl = new OrderServiceImpl(null, null);
		orderServiceImpl.createOrder(1L, "itemA", 10000);
	}
```

![](/assets/img/posts/102/1.png)

**이를 해결하기 위해서는 불필요하게 setter를 필드가 있는 클래스에 추가해야 한다.**

```java
	public void setMemberRepository(MemberRepository memberRepository) {
		this.memberRepository = memberRepository;
	}

	public void setDiscountPolicy(DiscountPolicy discountPolicy) {
		this.discountPolicy = discountPolicy;
	}
```

**그리고 테스트 코드에도 추가적으로 코드를 추가해야 한다.**

```java
	@Test
	public void fieldInjectionTest() {
		OrderServiceImpl orderServiceImpl = new OrderServiceImpl(null, null);
		orderServiceImpl.setMemberRepository(new MemoryMemberRepository());
		orderServiceImpl.setDiscountPolicy(new FixDiscountPolicy());
		orderServiceImpl.createOrder(1L, "itemA", 10000);
	}
```

**이럴 바에는 차라리 처음부터 수정자에 @Autowired를 하는 게 더 낫다...**

**수정자에 @Autowired를 붙이면 테스트 코드를 작성하기 쉽기 때문이다.** **즉, 필드 주입은 사용하지 말자.**

* * *

### **일반 메서드 주입**

**: 일반 메서드를 통해서 주입받을 수 있다.**

#### **특징**

**한 번에 여러 필드를 주입받을 수 있다. 일****반적으로 잘 사용하지 않는다.**

**코드를 통해서 알아보자.**

**생성자 주입과 사용하는 방법은 비슷하다. 그냥 메서드 위에 @Autowired를 붙이는 것이다.**

```java
	@Autowired
	public void init(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
		this.memberRepository = memberRepository;
		this.discountPolicy = discountPolicy;
	}
```

**당연한 이야기지만, 의존관계 자동 주입은 스프링 컨테이너가 관리하는 스프링 빈이어야 동작한다.**

**스프링 빈이 아닌 Member 같은 클래스에서 @Autowired 코드를 적용해도 아무 기능도 동작하지 않는다.**
