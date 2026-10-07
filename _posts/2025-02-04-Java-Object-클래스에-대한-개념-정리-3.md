---
title: "[Java] Object 클래스에 대한 개념 정리-3"
date: 2025-02-04 14:03:50 +0900
categories: ["Language", "Java"]
tags: ["equals()", "java equals", "java equals()", "java object equals()", "java object 개념"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-Object-%ED%81%B4%EB%9E%98%EC%8A%A4%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B0%9C%EB%85%90-%EC%A0%95%EB%A6%AC-3"
---
#### **equlas() - 1. 동일성과 동등성**

#### **Object는 동등성 비교를 위한 equals() 메서드를 제공**

-   **동일성(Identity)**  
    **\== 연산자를 사용해서 두 객체의 참조가 동일한 객체를 가리키고 있는지 확인**
-   **동등성(Equality)**  
    **equals() 메서드를 사용하여 두 객체가 논리적으로 동등한 지 확인**

> **동일성 ▶ 물리적으로 같은 메모리에 있는 객체 인스턴스인지 참조값을 확인하는 것, 자바 머신 기준**  
>   
> **동등성 ▶ 논리적으로(값) 같은지 확인하는 것, 사람이 생각하는 논리적인 기준**

```java
public class EqualsMainV1 {
	public static void main(String[] args) {
		UserV1 user1 = new UserV1("id-100");
		UserV1 user2 = new UserV1("id-100");

		System.out.println("identity = "+(user1 == user2) ); //동일성
		System.out.println("equality = "+(user1.equals(user2)) ); //동등성
	}
}
```

**이 경우 물리적으로 다른 메모리에 있는 다른 객체이지만, 회원 번호를 기준으로 생각해 보면 논리적으로는**

**같은 회원으로 볼 수 있다.** **즉, 동일성은 다르지만, 동등성은 같다.**

#### **예시코드**

```java
public class UserV1 {
	private String id;
	
	public UserV1(String id) {
		this.id = id;
	}
}
```

```java
public class EqualsMainV1 {
	public static void main(String[] args) {
		UserV1 user1 = new UserV1("id-100");
		UserV1 user2 = new UserV1("id-100");

		System.out.println("identity = "+(user1 == user2) ); //동일성
		System.out.println("equality = "+(user1.equals(user2)) ); //동등성
	}
}
```

#### **실행결과**

```java
 identity = false
 equality = false
```

#### **근데 왜 동등성도 false 일까?**

#### **Object.equals()**

**Object가 기본으로 제공하는 equals() == 으로 동일성 비교를 제공**

```java
public boolean equals(Object obj) {
    return (this == obj);
}
```

![](/assets/img/posts/307/1.png)

> **동등성이라는 개념은 각각의 클래스마다 다르다. 어떤 클래스는 주민등록번호를 기반으로 동등성 처리할 수 있고,**  
> **어떤 클래스는 고객의 연락처를 기반으로 동등성을 처리할 수 있다.**  
> **따라서 동등성 비교를 사용하고 싶으면 equals() 메서드를 재정의해야 한다. 그렇지 않으면 Object는 동일성 비교를 기본으로 제공한다.**

* * *

#### **equlas() - 2. 구현**

#### **예시코드**

**equlas()는 Object 타입을 매개변수로 사용한다. 따라서 객체의 특정 값을 사용하려면 다운캐스팅이 필요**

```java
public class UserV2 {
	private String id;
	
	public UserV2(String id) {
		this.id = id;
	}
    
	@Override
	public boolean equals(Object obj) {
		UserV2 user = (UserV2) obj;
		return id.equals(user.id);
	}
}
```

```java
public class EqualsMainV2 {
	public static void main(String[] args) {
		UserV2 user1 = new UserV2("id-100");
		UserV2 user2 = new UserV2("id-100");
		
		System.out.println("identity = " + (user1 == user2));;
		System.out.println("equality = " + (user1.equals(user2)));;

	}
}
```

#### **실행결과**

```java
 identity = false
 equality = true
```

**객체의 참조가 다르므로 동일성은 다르다.**

**하지만 user1, user2는 서로 다른 객체이지만 둘 다 같은 id(번호)를 가지고 있다.** **따라서 동등성은 같다.**

![](/assets/img/posts/307/2.png)

*동등성은 같다*

* * *

#### **정확한 equals() 구현**

**실제로 정확하게 동작하려면 다음과 같이 구현해야 한다. IDE는 정확한 equals() 코드를 자동으로 만들어준다**

```java
//변경 - 정확한 equals 구현, IDE 자동 생성
@Override
 public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;
    UserV2 user = (UserV2) o;
    return Objects.equals(id, user.id);
}
```

#### **equals() 메서드를 구현할 때 지켜야 하는 규칙**

1.  **반사성(Reflexivity)**  
    **객체는 자기 자신과 동등해야 한다. (x.equals(x)는 항상 true)**
2.  **대칭성(Symmetry)**  
    **두 객체가 서로에 대해 동일하다고 판단하면, 이는 양방향으로 동일해야 한다.**  
    **(x.equals(y)가 true이면 y.equals(x)도 true)**
3.  **추이성(Transitivity)**  
    **만약 한 객체가 두 번째 객체와 동일하고, 두 번째 객체가 세 번째 객체와 동일하다면, 첫 번째 객체는 세 번째 객체와도 동일해야 한다.**
4.  **일관성(Consistency)**  
    **두 객체의 상태가 변경되지 않는 한, equals() 메서드는 항상 동일한 값을 반환해야 한다.**
5.  **null에 대한 비교**  
    **모든 객체는 null과 비교했을 때, false를 반환해야 한다.**

> **참고**  
>   
> **참고로 동등성 비교가 항상 필요한 것은 아니다. 동등성 비교가 필요한 경우에만 equals()를 재정의한다.**  
> **equals()와 hashCode()는 보통 함께 사용된다.**
