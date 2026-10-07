---
title: "[Spring] Spring Boot - 입문(9) -  회원 도메인과 리포지터리 만들기 (회원관리 예제) + 람다식? + Optional 클래스?"
date: 2021-12-01 00:11:27 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["lambda", "optional 클래스", "람다식", "스프링", "스프링 부트", "스프링 부트 기본", "스프링 부트 입문"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Boot-%EC%9E%85%EB%AC%B88-%EB%B9%84%EC%A6%88%EB%8B%88%EC%8A%A4-%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%AD-%EC%A0%95%EB%A6%AC-%ED%9A%8C%EC%9B%90%EA%B4%80%EB%A6%AC-%EC%98%88%EC%A0%9C-%EB%9E%8C%EB%8B%A4%EC%8B%9D-Optional-%ED%81%B4%EB%9E%98%EC%8A%A4"
---
#### 회원 도메인과 리포지터리 만들기 + 람다식이란? + NPE를 방지해주는 Optional 클래스

* * *

1\. 회원 객체(Member)

```java
package hello.hellospring.domain;

public class Member {

	private Long id;
	private String name;
	
	
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
	
	
	
}
```

2\. MemberRepository

```java
package hello.hellospring.repository;

import java.util.List;
import java.util.Optional;

import hello.hellospring.domain.Member;

public interface MemberRepository {

	Member save(Member member); // 회원을 저장하면 저장된 회원 반환
	
	Optional<Member> findById(Long id); // id로 회원을 찾는 기능
	Optional<Member> findByName(String name); // name으로 회원을 찾는 기능
	
	//Optional - Java8에 들어간 기능 | findById , findByName에서 값이 null이면 Optional로 감싸서 처리한다. (나중에 더 디테일하게 설명)
	 
	List<Member> findAll();  // 지금까지 저장된 모든 회원 리스트 반환
}
```

3\. MemoryMemberRepository

```java
package hello.hellospring.repository;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Optional;

import hello.hellospring.domain.Member;

public class MemoryMemberRepository implements MemberRepository{

	
	private static Map<Long, Member> store = new HashMap<>(); 
	//실무에서는 동시성 문제가 있을 수 있어서 공유되는 변수는 ConcurrentHashMap을 쓰지만 예제니까 단순하게 HashMap사용
	private static long sequence = 8L;
	
	@Override
	public Member save(Member member) {
		member.setId(++sequence); // id는 시퀀스로 지정
		store.put(member.getId(), member); // 스토어에 저장
		return member;
	}

	@Override
	public Optional<Member> findById(Long id) {
		return Optional.ofNullable(store.get(id)); // if null인경우를 위해서 Optional로 감싸서 반환
	}

	@Override
	public Optional<Member> findByName(String name) {
		return store.values().stream() //Java8의 람다를 사용. 
				.filter(member -> member.getName().equals(name)) 
				// member에서 member.getName이 파라미터로 넘어온 name이 같은지 확인
				// 루프를 돌면서 하나를 찾으면 반환. 없으면 Optional을 감싸서 반환
				.findAny();
	}

	@Override
	public List<Member> findAll() {
		return new ArrayList<>(store.values()); // store에 있는 values를 반환
	}

}
```

#### **람다식(Lambda)**

-   함수명을 선언하고 사용하는 것이 아닌, **식별자 없이 실행가능한 함수**  
    즉, **메서드를 하나의 식으로 표현**하고, **메서드의 이름과 반환값이 없어**서 '**익명 함수'** 라고도 함
-   절차형 프로그래밍, 객체지향 프로그래밍과는 사뭇 다르게 함수의 구현과 호출만으로 프로그램을 만드는 방식인 함수형 프로그래밍에서 자주 사용
-   Java8부터 도입, 람다식을 익명함수를 만드는 것인데 익명함수는 **'1급객체'** 특징을 가지고 있음  
    **1급 객체 조건**  
    1\. 함수를 변수에 할당할 수 있어야 한다.  
    2\. 다른 함수의 파라미터로 함수 객체를 넘길 수 있어야 한다.  
    3\. 함수의 리턴값으로 함수 객체를 리턴할 수 있어야 한다.
-   **람다식을 다루기 위한 인터페이스 : 함수형 인터페이스**
-   **사용법 : (타입 매개변수, ...) -> {실행문 } ;  
    **매개변수 타입은 런타임시에 대입돠는 값에 따라 자동으로 인식되기 때문에 매개변수의 타입을 일반적으로 언급하지 X  
    하나의 매개변수만 있다면 괄호()를 생략할 수 있음  
    하나의 실행문만 있다면 중괄호{}도 생략할 수 있음
    
    ```java
    (타입 매개변수, ...) -> {실행문; } 
    1. (int a) -> {System.out.println(a)}; 
    2. (a) -> {System.out.println(a)}; 
    3. a -> System.out.println(a);
    
    출처: https://cornswrold.tistory.com/219 [평범한개발자노트]
    ```
    

#### **Optional**

-   Java8에서는 Optional<T> 클래스를 활용해 **NPE(NullPointException)을 방지**할 수 있도록 도와줌
-   **null이 올 수 있는 값을 감싸는 Wrapper 클래스**
-   Optional 클래스는 아래와 같은 value에 값을 저장하기 때문에 null이더라도 바로 NPE가 발생하지 않음
-   그리고 클래스이기 때문에 각종 메소드를 제공해줌

```java
public final class Optional<T> {
 
  // If non-null, the value; if null, indicates no value is present
  private final T value;
   
  ...
}
```

이렇게 작성된 코드가 과연 잘 돌아가는지? 확인하기 위해서는 Test코드를 작성해서 확인해야 한다.

다음 내용에서!!!
