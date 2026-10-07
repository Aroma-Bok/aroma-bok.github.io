---
title: "[Spring] Spring Framework - 핵심 원리 (5)  - 프로젝트 생성 + 순수 Java로만 구성 + 비즈니스 요구사항  + 설계 + 클래스 다이어그램 + 객체 다이어그램 + 다형성(Overridng,Overloading)"
date: 2022-01-10 23:14:29 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["impl", "구현체", "다형성", "스프링", "오버라이딩", "오버로딩", "인터페이스"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-5-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EC%83%9D%EC%84%B1-%EC%88%9C%EC%88%98-Java%EB%A1%9C%EB%A7%8C-%EA%B5%AC%EC%84%B1-%EB%B9%84%EC%A6%88%EB%8B%88%EC%8A%A4-%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%AD-%EC%84%A4%EA%B3%84-%ED%81%B4%EB%9E%98%EC%8A%A4-%EB%8B%A4%EC%9D%B4%EC%96%B4%EA%B7%B8%EB%9E%A8-%EA%B0%9D%EC%B2%B4-%EB%8B%A4%EC%9D%B4%EC%96%B4%EA%B7%B8%EB%9E%A8-%EB%8B%A4%ED%98%95%EC%84%B1OverridngOverloading"
---
### **프로젝트 생성 + 순수 Java로만 구성 + 비즈니스 요구사항  + 설계 + 클래스 다이어그램 + 객체 다이어그램 + 다형성(Overridng,Overloading)**

* * *

**스프링 부트 스터디 사이트에서 기본적으로만 사용되는 라이브러리 외 다른 건 사용하지 않고, '순수 Java'로 프로젝트를 먼저 만들어 보려고 한다. 이렇게 먼저 순수 Java로 진행해봐야 나중에 'Spring'을 사용하고 나서 어떤 차이가 있는지 제대로 공부해보자.**

**사용 IDE : 이클립스**

**언어 : Java11**

**[https://start.spring.io/](https://start.spring.io/)**

![](/assets/img/posts/62/1.png)

*기본 설정*

* * *

**build.gradle에서 'dependencies' 확인해보면 기본적인 2가지만 들어가 있는 것을 확인할 수 있다.**

```java
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```

**src에서 'Application'을 실행해보면 아래처럼 서버가 돌아가는 것을 확인할 수 있다.**

![](/assets/img/posts/62/2.png)

**나중에 '순수 Java'에서 'Spring'을 사용할 거라서 미리 지금 'Spring'을 세팅하는 것이다. 오해하지 말자.**

* * *

## **비즈니스 요구사항 확인 및 설계**

## **1\. 요구사항 확인**

#### **회원**

-   **회원을 가입하고 조회할 수 있다.**
-   **회원은 일반과 VIP 두 가지 등급이 있다.**
-   **회원 데이터는 자체 DB를 구축할 수 있고, 외부 시스템과 연동할 수 있다. (미확정)**

#### **주문과 할인 정책**

-   **회원은 상품을 주문할 수 있다.**
-   **회원 등급에 따라 할인 정책을 적용할 수 있다.**
-   **할인 정책은 모든 VIP는 1000원을 할인해주는 고정 금액 할 일은 적용해달라. (나중에 변경될 수 있다.)**
-   **할인 정책은 변경 가능성이 높다. 회사의 기본 할인 정책을 아직 정하지 못했고, 오픈 직전까지 고민을 미루고 싶다. 최악의 경우 할인을 적용하지 않을 수 도 있다.**

**요구사항을 보면 회원 데이터, 할 일 정책 같은 부분은 지금 결정하기 어려운 부분이 있다. 그렇다고 이런 정책이 결정될 때까지 개발을 무기한 기다릴 수 도 없다. 앞에서 배운 객체 지향 설계 방법을 생각해보자!**

#### **인터페이스를 만들고 구현체를 언제든지 갈아 끼울 수 있도록 설계하면 된다!! (=역할과 구분을 나누자!!)**

## **2\. 회원 도메인 설계**

#### **회원 도메인 요구사항**

-   **회원을 가입하고 조회할 수 있다.**
-   **회원은 일반과 VIP 두 가지 등급이 있다.**
-   **회원 데이터는 자체 DB를 구축할 수 있고, 외부 시스템과 연동할 수 있다. (미확정)**

![](/assets/img/posts/62/3.png)

*회원 도메인 협력 관계*

![](/assets/img/posts/62/4.png)

*회원 클래스 다이어그램*

**역할 : 인터페이스 - MemberRepository**

**구현체 : 메모리 회원 저장소 / DB 회원 저장소 / 외부 시스템 연동 회원 저장소**

![](/assets/img/posts/62/5.png)

*회원 객체 다이어그램*

**( 회원 서비스 : MemverServiceImpl )**

**※ 객체 다이어그램 : 객체 간의 메모리 참조**

**개발자는 회원 도메인 협력관계를 구체화해서 회원 클래스 다이어그램을 만들어낸다.**

더보기

#### **클래스 다이어그램**

-   **시스템의 정적 보기를 나타내며, 클래스의 속성과 작업을 설명한다. 클래스 다이어그램은 객체 지향 언어로 직접 매핑할 수 있기 때문에 객체 지향 시스템에서 가장 널리 사용되는 모델링 다이어그램이다.**
-   **구현체는 동적으로 결정되기 때문에, 그림만 봐서는 확인할 수가 없다. 즉, 서버를 구현하지 않고 확인이 불가능하다.**

#### **객체 다이어그램**

-   **객체에 중점을 두며, 객체 다이어그램의 기본 개념은 클래스 다이어그램과 유사하다. 이러한 다이어그램은 특정 순간의 개체 동작과 그 관계를 이해하는 데 도움이 된다.**
-   **서버가 구현되고 나서 돌아가는 관계를 표현. 즉, 클래스 다이어그램에서 확인하지 못한 것을 디테일하게 확인 가능한 다이어그램.**

* * *

## **회원 도메인 개발**

## **백문이 불 여일 타, 꼭 같이 코딩하면서 공부하자!!!**

#### **1\. member폴더 생성 및 'Grade - Enum' & 'Member - Class' 생성**

```java
package hello.core.member;

public class Member {
	
	private Long id;
	private String name;
	private Grade grade;
	
	//생성자 생성
	public Member(Long id, String name, Grade grade) {
		super();
		this.id = id;
		this.name = name;
		this.grade = grade;
	}

	public Long getId() {
		return id;
	}

	public void setId(Long id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public Grade getGrade() {
		return grade;
	}

	public void setGrade(Grade grade) {
		this.grade = grade;
	}
	
}
```

```java
package hello.core.member;

public enum Grade {
	
	//회원을 2가지 등급으로 나눈다.
	BASIC,
	VIP
}
```

**Private은 Getter & Setter에 의해서 값을 세팅할 수 있게 된다.**

**생성자 및 Getter & Setter 단축키: Alt + Shift + S**

#### **2\. MemberRepository 생성 - 인터페이스**

```java
public interface MemberRepository {

	void save(Member member); // 저장하는 기능
	
	Member findById(Long memberid); // 아이디로 회원을 찾는 기능
}
```

#### **3\. MemoryMemberRepository 생성 - 구현체**

**※ 원래는 동시성 이슈로 인해서, 'ConcurrentHashMap'을 사용해야 하지만, 예제를 위해 'HashMap'을 사용**

```java
public class MemoryMemberRepository implements MemberRepository {

	private static Map<Long, Member> store = new HashMap<>();
    
	@Override
	public void save(Member member) {
		store.put(member.getId(), member); 
	}
	@Override
	public Member findById(Long memberid) {
		return store.get(memberid);
	}
}
```

#### **4\. MemberService 생성 - 인터페이스**

```java
public interface MemberService {
	
	void join(Member member);
	
	Member findMember(Long memberid);
}
```

#### **5\. MemberServiceImpl - 구현체**

**회원이 가입하고 찾을라고 한다면, 먼저 데이터가 있는 'Repository'가 필요하므로 구현체를 갖고 온다.**

**그리고 'MemberService'의 'save'를 호출하면, '다형성 - Override'에 의해서 'MemoryMemberRepository'안에 있는 'save'가 호출이 된다.**

```java
public class MemberServiceImpl implements MemberService{

private final MemberRepository memberRepository = new MemoryMemberRepository();
	
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

**\* 다형성 : 하나의 클래스나 함수가 다양한 방식으로 동작이 가능한 것을 말한다.**

**e.g - 오버 라이딩(Overriding) , 오버 로딩(Overloading)**
