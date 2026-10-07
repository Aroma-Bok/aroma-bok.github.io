---
title: "[Spring] Spring Framework - 핵심 원리 (24) - 싱글톤 컨테이너, 스프링 컨테이너"
date: 2022-02-08 23:15:37 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["@configuration", "스프링 컨테이너", "싱글톤 컨테이너"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-24-%EC%8B%B1%EA%B8%80%ED%86%A4-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EC%8A%A4%ED%94%84%EB%A7%81-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88"
---
### **싱글톤 컨테이너, 스프링 컨테이너**

* * *

-   **스프링 컨테이너는 싱글톤 패턴의 문제점을 해결하면서, 객체 인스턴스를 싱글톤(1개만 생성)으로 관리한다.**
-   **지금까지 정리했던 내용에서의 스프링 빈이 바로 싱글톤으로 관리되는 빈이다.**

#### **싱글톤 컨테이너 (=스프링 컨테이너)**

-   **스프링 컨테이너는 싱글턴 패턴을 적용하지 않아도, 객체 인스턴스를 싱글톤으로 관리한다.**
-   **전에 설명한 컨테이너 생성 과정을 자세히 보자. 컨테이너는 객체를 하나만 생성해서 관리한다.**  
    
    ```java
    @Configuration
    public class AppConfig {
    	@Bean
    	public MemberService memberService() {
    		//return new MemberServiceImpl(new MemoryMemberRepository()); //'MemoryMemberRepository'객체의 참조값을 'MemberServiceImpl'에 넣어준다.
    		return new MemberServiceImpl(memberRepository());
    	}
    	@Bean
    	public MemberRepository memberRepository() {
    		return new MemoryMemberRepository();
    	}
    	@Bean
    	public OrderService orderService() {
    		return new OrderServiceImpl(memberRepository(), discountPolicy());
    	}
    	@Bean
    	public DiscountPolicy discountPolicy() {
    		return new FixDiscountPolicy();
    		//return new RateDiscountPolicy();
    	}
    }​
    ```
    
    <table style="border-collapse: collapse; width: 99.6457%; height: 135px;" border="1" data-ke-align="alignLeft" data-ke-style="style12"><tbody><tr style="height: 25px;"><td style="width: 50%; height: 25px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">빈 이름</span></b></td><td style="width: 50%; height: 25px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">빈 객체</span></b></td></tr><tr style="height: 25px;"><td style="width: 50%; height: 25px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">memberService</span></b></td><td style="width: 50%; height: 25px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">MemberServiceImpl</span></b></td></tr><tr style="height: 25px;"><td style="width: 50%; height: 25px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">orderService</span></b></td><td style="width: 50%; height: 25px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">OrderServiceImpl</span></b></td></tr><tr style="height: 25px;"><td style="width: 50%; height: 25px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">memberRepository</span></b></td><td style="width: 50%; height: 25px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">MemoryMemberRepository</span></b></td></tr><tr style="height: 25px;"><td style="width: 50%; height: 25px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">discountPolicy</span></b></td><td style="width: 50%; height: 25px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">FixDiscountPolicy</span></b></td></tr></tbody></table>
    
-   **스프링 컨테이너는 싱글톤 컨테이너 역할을 한다. 이렇게 싱글톤 객체를 생성하고 관리하는 기능을**  
    **싱글톤 레지스트리라 한다.**
-   **스프링 컨테이너의 이런 기능 덕분에 싱글턴 패턴의 모든 단점을 해결하면서 객체를 싱글톤으로 유지할 수 있다.**
-   **싱글톤 패턴을 위한 지저분한 코드가 들어가지 않아도 된다.**
-   **DIP, OCP, 테스트, private 생성자로부터 자유롭게 싱글톤을 사용할 수 있다.**

**코드로 확인해보자. 기존 테스트 코드에 아래 코드를 입력해서 확인하자.**

```java
	@Test
	@DisplayName("스프링 컨테이너와 싱글톤")
	public void springContainer() {
		ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
		
		//1. 조회 : 호출할 때 마다 객체를 생성
		MemberService memberService1 = ac.getBean("memberService", MemberService.class);
		
		//1. 조회 : 호출할 때 마다 객체를 생성
		MemberService memberService2 = ac.getBean("memberService", MemberService.class);
		
		//참조값이 같은 것을 확인.
		System.out.println("memberService1 = " + memberService1);
		System.out.println("memberService2 = " + memberService2);
		
		// memberService1 != memberService2
		Assertions.assertThat(memberService1).isSameAs(memberService2);
	}
```

**결과를 확인해보면 같은 객체를 생성하는 것을 확인할 수 있다.** 

```java
memberService1 = hello.core.member.MemberServiceImpl@226642a5
memberService2 = hello.core.member.MemberServiceImpl@226642a5
```

![](/assets/img/posts/87/1.png)

* * *

#### **싱글톤 컨테이너 적용 후**

**아래 MemberServiceImpl에는 싱글톤과 관련된 코드가 하나도 들어가 있지 않다.**

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
	}

	@Override
	public Member findMember(Long memberid) {
		return memberRepository.findById(memberid);
	}
}
```

**그림으로 설명하면 아래와 같다. 3명의 각각의 클라이언트들이 MemberService를 요청하면 동일한 MemberService를 반환해서 하나의 객체의 참조값만 전달한다.**

![](/assets/img/posts/87/2.png)

**▶ 스프링 컨테이너 덕분에 고객의 요청이 올 때마다 객체를 생성하는 것이 아니라,  
    이미 만들어진 객체를 공유해서 효율적으로 재사용할 수 있다.**

**처음엔 스프링 컨테이너를 사용하면서 장점을 잘 몰랐다.**  
**하지만, 이렇게 스프링 컨테이너로 바꿔가면서 뭐가 좋은지 조금씩 정리하면서 느낄 수 있을 것이다.**

**싱글톤을 그냥 만들어서 사용해도 되지만, 언제 만들어서 사용하며, 더 복잡할 것이다.**  
**그래서 스프링 기능을 사용해서 쉽게 싱글톤의 기능을 사용할 수 있게 된다.**

> **참고**   
> **스프링의 기본 빈 등록 방식은 싱글톤이지만, 싱글톤 방식만 지원하는 것은 아니다. 요청할 때마다 새로운 객체를 생성해서 반환하는 기능도 제공한다. 자세한 내용은 추후에 빈 스코프 내용을 정리하면서 알아보자.**  
> **(특별한 경우를 제외하고는, 거의 99%는 기본 빈 등록 방식을 사용한다고 한다.)**

**다음 내용은 싱글톤 방식의 주의점을 공부해보자. 너무나도 중요한 내용이라고 한다.**
