---
title: "[Spring] Spring Framework - 핵심 원리 (13) - IOC, DI, 컨테이너 + 프레임워크 vs 라이브러리 차이점 + 제어의 역전"
date: 2022-01-21 00:06:48 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["di 컨테이너", "ioc", "동적인 객체 인스턴스 의존관계", "스프링 프레임워크", "정적인 클래스 의존관계", "제어의 역전", "컨테이너", "프레임워크 라이브러리 차이점"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-13-IOC-DI-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%ED%94%84%EB%A0%88%EC%9E%84%EC%9B%8C%ED%81%AC-vs-%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC-%EC%B0%A8%EC%9D%B4%EC%A0%90-%EC%A0%9C%EC%96%B4%EC%9D%98-%EC%97%AD%EC%A0%84"
---
## **IOC, DI, 컨테이너 + 프레임워크 vs 라이브러리 차이점  + 제어의 역전**

* * *

**제어의 역전, IOC 이런 단어를 많이 들어봤을 것이다. 이것은 '스프링'에만 국한되는 것이 아니다.**

**보통은 개발자가 원하는 대로 객체를 생성하고, 그 안에서 생성하고 호출하고 이런 식으로 진행한다.**

**즉, 개발자가 직접 제어한다.**

**하지만 제어의 역전의 개념은 내가 호출하는 게 아니라, 프레임워크 같은 게 나 대신에 코드를 호출해주는 것.**

* * *

### **제어의 역전 IoC(Inversion of Control)**

-   **기존 프로그램은 클라이언트 구현 객체가 스스로 필요한 서버 구현 객체를 생성하고, 연결하고, 실행했다.**   
    **한마디로 구현 객체가 프로그램의 제어 흐름을 스스로 조종했다. 개발자 입장에서는 자연스러운 흐름이다.**   
    
    ```java
    public class OrderServiceImpl implements OrderService{
    	private final MemberRepository memberRepository = new MemoryMemberRepository();
    	private final DiscountPolicy discountPolicy = new FixDiscountPolicy();​
    ```
    
-   **반면에 AppConfig가 등장한 이후에 구현 객체는 자신의 로직을 실행하는 역할만 담당한다. 프로그램의 제어 흐름은 이제 AppConfig가 가져간다. 예를 들어서 OrderServiceImpl은 필요한 인터페이스들을 호출하지만 어떤 구현 객체들이 실행될지 모른다.**  
    
    ```java
    public class OrderServiceImpl implements OrderService{
    	private final MemberRepository memberRepository;
    	private final DiscountPolicy discountPolicy;​
    ```
    
-   **프로그램에 대한 제어 흐름에 대한 권한은 모두 AppConfig가 가지고 있다. 심지어 OrderServiceImpl 도**  
    **AppConfig가 생성한다. 그리고 AppConfig는 OrderServiceImpl이 아닌 OrderService 인터페이스의 다른 구현 객체를 생성하고 실행할 수도 있다. 그런 사실도 모른 체 OrderServiceImpl은 묵묵히 자신의 로직을 실행할 뿐이다.**  
    
    ```java
    public class AppConfig {
    	public MemberService memberSevice() {
    		return new MemberServiceImpl(memberRepository());
    	}
    	private MemberRepository memberRepository() {
    		return new MemoryMemberRepository();
    	}
    	public OrderService orderService() {
    		return new OrderServiceImpl(memberRepository(), discountPolicy());
    	}
    	private DiscountPolicy discountPolicy() {
    		return new FixDiscountPolicy();
    	}
    }​
    ```
    
-   **이렇듯 프로그램의 제어 흐름을 직접 제어하는 것이 아니라 외부에서 관리하는 것을 제어의 역전(IoC)이라 한다.**

### **프레임워크 vs 라이브러리 차이점**

-   **프레임워크가 내가 작성한 코드를 제어하고, 대신 실행하면 그것은 프레임워크가 맞다. (e,g : JUnit)**  
    **로직만 개발할 뿐, 실행과 제어는 '스프링 프레임워크'가 제어한다.**   
    
    ```java
    	@Test
    	void join() {
    		//given : 무언가 주어졌을 때
    		Member member = new Member(1L, "memberA", Grade.VIP);
    		
    		//when : ~할 때
    		memberService.join(member);
    		Member findMember = memberService.findMember(1L); // 내가 찾은 것
    		
    		//then : 결과가 ~다
    		Assertions.assertThat(member).isEqualTo(findMember); // 'Assertions'통해서 찾은 것 
    	}​
    ```
    
-   **반면에 내가 작성한 코드가 직접 제어의 흐름을 담당한다면 그것은 프레임워크가 아니라 라이브러리다.**

### **의존관계 주입 DI(Dependency Injection)**

-   **OrderServiceImpl은 DiscountPolicy 모른다.**  
    **\=> 인터페이스에만 의존하고 있어서, FixDiscountPolicy가 사용될지 RateDiscountPolicy가 사용될지 모른다.**
-   **의존관계는 정적인 클래스 의존관계와, 실행 시점에 결정되는 동적인 객체(인스턴스) 의존관계 둘을 분리해서 생각해야 한다**

* * *

### **정적인 클래스 의존관계**

**클래스가 사용하는 import 코드만 보고 의존관계를 쉽게 판단할 수 있다.** 

**정적인 의존관계는 애플리케이션을 실행하지 않아도 분석할 수 있다.** 

**클래스 다이어그램을 보자  
**

**OrderServiceImpl은 MemberRepository, DiscountPolicy에 의존한다는 것을 알 수 있다.  
그런데 이러한 클래스 의존관계만으로는 실제 어떤 객체가 OrderServiceImpl에 주입될지 알 수 없다.**

![](/assets/img/posts/74/1.png)

**아래 코드를 보면서 다시 한번 설명을 하면, 'OrderServiceImpl'은 'OrderService'을 상속받고,**

**'MemberRepository'와 'DiscountPolicy'를 참조하는 것을 알 수 있다.**

**하지만, 'OrderServiceImpl'생성자에서는 'MemberRepository'와 'DiscountPolicy'에 무엇이 들어오는 지는 알 수 없다. 실행을 시켜봐야지만 확인이 가능하다.**

```java
public class OrderServiceImpl implements OrderService{
	private final MemberRepository memberRepository;
	private final DiscountPolicy discountPolicy; // 추상화인 '인터페이스'에만 의존한다.

	public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
		super();
		this.memberRepository = memberRepository;
		this.discountPolicy = discountPolicy;
	}
}
```

### **동적인 객체 인스턴스 의존관계**

**애플리케이션 실행 시점에 실제 생성된 객체 인스턴스의 참조가 연결된 의존관계다.**

![](/assets/img/posts/74/2.png)

-   **애플리케이션 '실행 시점(런타임)'에 외부에서 실제 구현 객체를 생성하고 클라이언트에 전달해서 클라이언트와 서버의 실제 의존관계가 연결되는 것을 의존관계 주입이라 한다.**
-   **객체 인스턴스를 생성하고, 그 참조값을 전달해서 연결된다.**  
    
    ```java
    public class OrderServiceImpl implements OrderService{
    	private final MemberRepository memberRepository;
    	private final DiscountPolicy discountPolicy; 
        // => 위 참조하는 곳에 값을 전달해서 연결시킨다.​
    ```
    
-   **의존관계 주입을 사용하면 클라이언트 코드를 변경하지 않고, 클라이언트가 호출하는 대상의 타입 인스턴스를 변경할 수 있다.**
-   **의존관계 주입을 사용하면 정적인 클래스 의존관계를 변경하지 않고, 동적인 객체 인스턴스 의존관계를 쉽게 변경할 수 있다.**

### **IoC -Inversion of Control 컨테이너, DI - Dependency Injection 컨테이너**

-   **AppConfig처럼 객체를 생성하고 관리하면서 의존관계를 연결해주는 것을 'IoC 컨테이너' 또는 'DI 컨테이너'라 한다.**  
    **즉, 'APPConfig'가 애플리케이션 전체를 제어하고 결정한다. ('APPConfig'에 의해서 '제어의 역전'이 발생하는 것!)**
-   **의존관계 주입에 초점을 맞추어 최근에는 주로 DI 컨테이너라 한다.**
-   **또는 어셈블러, 오브젝트 팩토리 등으로 불리기도 한다.**

```java
public class AppConfig {
	public OrderService orderService() {
		return new OrderServiceImpl(memberRepository(), discountPolicy());
	}
}
```

**APPConfig가 'OrderServiceImpl'를 만들 때, 'memberRepository'와 'discountPolicy'를 집어넣어주고 있다.**

#### **다음에는 '스프링'을 이용해서 차이점을 파악해보자!!**
