---
title: "[Spring] Spring Framework - 핵심 원리 (39) -조회한 빈이 모두 필요할 때, List, Map + 다형성 활용 + 동일한 타입의 스프링 빈이 다 필요한 경우"
date: 2022-03-22 00:20:43 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["다형성 활용", "동일한 타입의 스프링 빈이 다 필요한 경우", "모든 빈 조회", "스프링 빈 조회"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-39"
---
### **조회한 빈이 모두 필요할 때, List, Map + 다형성 활용 + 동일한 타입의 스프링 빈이 다 필요한 경우**

* * *

**의도적으로 정말 해당 타입의 스프링 빈이 다 필요한 경우도 있다.**

**예를 들어서, 할인 서비스를 제공하는데, 클라이언트가 할인의 종류(rate, fix)를 선택할 수 있다고**

**가정해보자.**

**스프링을 사용하면 소위 말하는 전략 패턴을 매우 간단하게 구현할 수 있다.**

**코드로 살펴보자.**

* * *

#### **1\. 테스트 코드 생성**

```java
public class AllBeanTest {

	@Test
	public void findAllBean() {
		ApplicationContext ac = new AnnotationConfigApplicationContext(AutoAppConfig.class,DiscountService.class);
		//스프링 빈으로 등록
		
	}
	
	static class DiscountService{
		private final Map<String, DiscountPolicy> policyMap;
		private final List<DiscountPolicy> policies;
        
		@Autowired
		public DiscountService(Map<String, DiscountPolicy> policyMap, List<DiscountPolicy> policies) {
			this.policyMap = policyMap;
			this.policies = policies;
			System.out.println("policyMap = " + policyMap);
			System.out.println("policies = " + policies);
		}
	}
}
```

#### **2\. AutoAppConfig에서 ComponentScan을 실행**

```java
@Configuration
@ComponentScan(
		basePackages = "hello.core.member",
		basePackageClasses = AutoAppConfig.class,
		excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)) // -> 컴포넌트 스캔을 하면서, 제외할 것을 지정해주는 것 

public class AutoAppConfig {

}
```

#### **3\. RateDiscountPolicy, FixDiscountPolicy 확인하면서 의존관계 자동 주입**

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

#### **4\. 결과 확인**

**policyMap은 key값과 value값이 각각 들어오는 것을 확인할 수 있다.**

**policies는 인스턴스가 바로 들어온다.**

```java
policyMap = {fixDiscountPolicy=hello.core.discount.FixDiscountPolicy@5f031ebd, rateDiscountPolicy=hello.core.discount.RateDiscountPolicy@4ee37ca3}
policies = [hello.core.discount.FixDiscountPolicy@5f031ebd, hello.core.discount.RateDiscountPolicy@4ee37ca3]
```

#### **5\. 검증**

**테스트 코드에 검증을 위해서 Assertion의 asserThat을 사용하자.**

**그리고 String으로 해당 빈을 불러올 수 있는 다형성 코드를 만들어보자.**

```java
public class AllBeanTest {

	@Test
	public void findAllBean() {
		ApplicationContext ac = new AnnotationConfigApplicationContext(AutoAppConfig.class,DiscountService.class);
		//스프링 빈으로 등록
		
		DiscountService discountService = ac.getBean(DiscountService.class);
		Member member = new Member(1L, "userA", Grade.VIP);
		int discountPrice = discountService.discount(member,10000,"fixDiscountPolicy");
		
		Assertions.assertThat(discountService).isInstanceOf(DiscountService.class);
		Assertions.assertThat(discountPrice).isEqualTo(1000);
		
		int ratediscountPrice = discountService.discount(member,10000,"rateDiscountPolicy");
		Assertions.assertThat(ratediscountPrice).isEqualTo(1000);
	}
	
	static class DiscountService{
		private final Map<String, DiscountPolicy> policyMap;
		private final List<DiscountPolicy> policies;
		
		@Autowired
		public DiscountService(Map<String, DiscountPolicy> policyMap, List<DiscountPolicy> policies) {
			this.policyMap = policyMap;
			this.policies = policies;
			System.out.println("policyMap = " + policyMap);
			System.out.println("policies = " + policies);
		}

		public int discount(Member member, int price, String discountCode) { //할인코드를 빈 이름이랑 매칭
			DiscountPolicy discountPolicy = policyMap.get(discountCode);
			
			System.out.println("discountCode = " + discountCode);
			System.out.println("discountPolicy = " + discountPolicy);
			
			return discountPolicy.discount(member, price);
		}
	}
}
```

![](/assets/img/posts/114/1.png)

```java
discountCode = fixDiscountPolicy
discountPolicy = hello.core.discount.FixDiscountPolicy@2bb7bd00
discountCode = rateDiscountPolicy
discountPolicy = hello.core.discount.RateDiscountPolicy@5f031ebd
```

* * *

#### **로직 분석**

**DiscountService는 Map으로 모든 DiscountPolicy를 주입받는다. 이때 fixDiscounfPolicy, rateDiscounfPolicy가 주입된다.**

**discount 메서드는 discountCode로 "fixDiscounfPolicy"가 넘어오면서 map에서 "fixDiscounfPolicy"**

**스프링 빈을 찾아서 실행한다.**

**물론 "rateDiscounfPolicy"가 넘어오면 "rateDiscounfPolicy" 스프링 빈을 찾아서 실행한다.**

#### **주입 분석**

**Map <String, DiscountPolicy> : Map의 key에 스프링 빈의 이름을 넣어주고, 그 값으로 DiscountPolicy**

**타입으로 조회한 모든 스프링 빈을 담아준다.**

**List <DiscountPolicy> : DiscountPolicy 타입으로 조회한 모든 스프링 빈을 담아준다.**

**만약 해당하는 타입의 스프링 빈이 없으면, 빈 컬렉션이나 Map을 주입한다.**

> **참고**  
> **스프링 컨테이너는 생성자에 클래스 정보를 받는다. 여기에 클래스 정보를 넘기면 해당 클래스가 스프링 빈으로 자동 등록된다.  
>   
> \=> new AnnotationConfigApplicationContext(AutoAppConfig.class, DiscountService.class);**  
>   
> **이 코드는 2가지로 나누어 이해할 수 있다.  
> **  
> **1\. new AnnotationConfigApplicationContext()를 통해 스프링 컨테이너를 생성한다.**  
> **2\. AutoAppConfig.class, DiscountService.class를 파라미터로 넘기면서 해당 클래스를 자동으로 스프링 빈으로 등록한다.**

**정리하면 스프링 컨테이너를 생성하면서, 해당 컨테이너에 동시에 AutoAppConfig, DiscountService를**

**스프링 빈으로 자동 등록한다.**
