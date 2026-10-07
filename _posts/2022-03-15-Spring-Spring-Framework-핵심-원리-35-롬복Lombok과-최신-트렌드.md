---
title: "[Spring] Spring Framework - 핵심 원리 (35) - 롬복(Lombok)과 최신 트렌드"
date: 2022-03-15 23:51:17 +0900
categories: ["Framework & Library"]
tags: ["@requiredargsconstructor", "getter", "lombok", "lombok 역할", "setter", "롬복"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-35-%EB%A1%AC%EB%B3%B5Lombok%EA%B3%BC-%EC%B5%9C%EC%8B%A0-%ED%8A%B8%EB%A0%8C%EB%93%9C"
---
### **롬복(Lombok)과 최신 트렌드 + Getter, Setter + 롬복 설치방법 + 롬복 역할**

* * *

**최근 SQLD 자격증을 공부하느라 스프링 공부를 제대롤 하지 못했다. 다시 공부해보자!**

#### **롬복(Lombok)과 최신 트렌드**

**막상 개발을 해보면, 대부분이 다 불변이고, 그래서 다음과 같이 생성자에 final 키워드를 사용하게 된다.**

**그런데 생성자도 만들어야 하고, 주입받은 값을 대입하는 코드도 만들어야 하고..**

**필드 주입처럼 좀 편리하게 사용하는 방법은 없을까?**

**역시 개발자는 귀찮은 것은 못 참는다...!**

**다음 기본 코드를 최적화해보자.**

```java
@Component
public class OrderServiceImpl implements OrderService{

     private final MemberRepository memberRepository;
     private final DiscountPolicy discountPolicy; // 추상화인 '인터페이스'에만 의존한다.
		
	@Autowired 
	public OrderServiceImpl(MemberRepository memberRepository,DiscountPolicy discountPolicy) {
		this.memberRepository = memberRepository;
		this.discountPolicy = discountPolicy; 
	}
```

**1\. 생성자가 1개만 존재할 때는, @Autowierd를 생략할 수 있다. (@Component를 사용한다는 조건 하)**

```java
@Component
public class OrderServiceImpl implements OrderService{

     private final MemberRepository memberRepository;
     private final DiscountPolicy discountPolicy; // 추상화인 '인터페이스'에만 의존한다.
		
	public OrderServiceImpl(MemberRepository memberRepository,DiscountPolicy discountPolicy) {
		this.memberRepository = memberRepository;
		this.discountPolicy = discountPolicy; 
	}
```

**2\. 롬복(Lombok) 적용**

**build.gradle에 아래처럼 롬복(Lombok) 관련 Dependency를 추가하자.**

**이렇게 작성하면, 자동으로 Dependency를 다운로드하여온다.** 

```java
plugins {
	id 'org.springframework.boot' version '2.6.2'
	id 'io.spring.dependency-management' version '1.0.11.RELEASE'
	id 'java'
}

group = 'hello'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '11'

repositories {
	mavenCentral()
}

//lombok 설정 추가 시작
configurations {     compileOnly {
        extendsFrom annotationProcessor
} }
//lombok 설정 추가 끝

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter'
    
    //lombok 라이브러리 추가 시작
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'     
    
    testCompileOnly 'org.projectlombok:lombok'
    testAnnotationProcessor 'org.projectlombok:lombok'
	//lombok 라이브러리 추가 끝
    
    
    
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

test {
	useJUnitPlatform()
}
```

* * *

#### **★ Gradle Refresh 방법 ★**  
**해당 프로젝트 우클릭 -> Gradle -> 'Refresh Gradle Project' 클릭**

![](/assets/img/posts/109/1.png)

*Refresh하면 해당 Dependency를 다운*

* * *

> **참고**   
> **start.spring.io 에서 처음 Spring을 생성할 때, 추가할 수 있는 라이브러리이다. 알아두자.**

![](/assets/img/posts/109/2.png)

* * *

#### **Lombok(롬복) 설치 참고** 

**1\. [https://projectlombok.org/download](https://projectlombok.org/download) 접속해서 다운**

**2\. eclipse가 설치되어있는 폴더로 다운로드한 jar파일을 옮긴다.**

**3\. cmd 실행 -> jar를 이동시킨 폴더로 이동 -> java -jar lombok.jar 실행**

![](/assets/img/posts/109/3.png)

![](/assets/img/posts/109/4.png)

* * *

**3\. 롬복(Lombok) 사용법**

**일단 아무 Class나 만들고, @Setter, @Getter 어노테이션을 적용해보자.**

```java
@Getter
@Setter
public class helloLombok {

	private String name;
	private int age;
	
	public static void main(String[] args) {
		helloLombok hellolombok = new helloLombok();
		hellolombok.setName("신기하다");
		
		System.out.println("hellolombok = " + hellolombok);
	}
}
```

![](/assets/img/posts/109/5.png)

* * *

**롬복(Lombok)의 역할?**

**귀찮게 아래처럼 Set메서드, Get메서드를 작성하지 않아도 된다.**

**@Setter :  Set 메서드 대체**

**@Getter : Get 메서드 대체**

**@ToString : toString 메서드 대체하면서 쉽게 쉽게 사용할 수 있다.**

```java
	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public int getAge() {
		return age;
	}

	public void setAge(int age) {
		this.age = age;
	}
```

**실무에서 진짜 많이 쓴다! 생성자 관련 어니 테이션도 있으니 인터넷에서 검색해보면서 공부하면 좋을 것 같다.**

* * *

**다시 롬복을 실제 작성했던 코드에 적용해보자.**

**4\. OrderServiceImpl에 적용**

**기존 아래의 코드에 @RequiredArgsConstructor를 적용해서 생성자를 대체해보자.**

```java
@Component
public class OrderServiceImpl implements OrderService{
	
	 private final MemberRepository memberRepository;
     private final DiscountPolicy discountPolicy; // 추상화인 '인터페이스'에만 의존한다.
		
	@Autowired 
	public OrderServiceImpl(MemberRepository memberRepository,DiscountPolicy discountPolicy) {
		this.memberRepository = memberRepository;
		this.discountPolicy = discountPolicy; 
	}
```

**※ @RequiredArgsConstructor는 필수 생성자를 대신 만들어준다.**

**(final 키워드를 사용하면 반드시 값이 대입되어야 하므로, 필수로 여겨진다.)**

**위 코드가 아래처럼 간결해지고 깔끔해진다. 가장 편함을 느낄 때는, 의존관계를 추가할 때가 가장 편하다.**

```java
@Component
@RequiredArgsConstructor
public class OrderServiceImpl implements OrderService{

	 private final MemberRepository memberRepository;
     private final DiscountPolicy discountPolicy; // 추상화인 '인터페이스'에만 의존한다.
```

**이 최종 결과 코드와 이전의 코드는 완전히 동일하다. 롬복이 자바의 어노테이션 프로세서라는 기능을 이용해서 컴파일 시점에 생성자 코드를 자동으로 생성해준다. 실제 class를 열어보면 생성자가 추가되어 있는 것을 확인할 수 있다.**

* * *

#### **정리**

**최근에는 생성자를 딱 1개 두고, @Autowired를 생략하는 방법을 주로 사용한다.**

**여기에 Lombok 라이브러리의 @RequiredArgsConstructor를 함께 사용하면 기능은 다 제공하면서 코드는 깔끔하게 사용할 수 있다.**
