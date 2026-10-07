---
title: "[Java] Object 클래스에 대한 개념 정리-1"
date: 2025-02-04 10:54:37 +0900
categories: ["Language", "Java"]
tags: ["java object", "김영한 object", "김영한 중급 자바", "인프런 object", "자바 object"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-Object-%ED%81%B4%EB%9E%98%EC%8A%A4%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B0%9C%EB%85%90-%EC%A0%95%EB%A6%AC-1"
---
#### **java.lang 패키지**

-   **Java가 기본적으로 제공하는 라이브러리(클래스 모음) 중에 가장 기본이 되는 패키지**
-   **lang은 Language의 줄임말**
-   **Java 언어를 이루는 가장 기본이 되는 클래스들을 보관하는 패키지**

#### **java.lang 패키지의 대표적인 클래스**

-   **Object**  
    **모든 Java 객체의 부모 클래스**
-   **String**  
    **문자열**
-   **Integer, Long, Double**  
    **Wrapper타입, 기본형 데이터 타입을 객체로 만든 것**
-   **Class**  
    **클래스 메타 정보**
-   **System**  
    **시스템과 관련된 기본 기능들을 제공**

> **import 생략 가능**  
>   
> **java.lang 패키지는 모든 자바 애플리케이션에 자동으로 import 된다. 따라서 구문을 사용하지 않아도 된다**

* * *

#### **Obejct 클래스**

-   **Java에서 모든 클래스의 최상위 부모 클래스는 항상 Object 클래스**

#### **예시 코드**

```java
// 부모가 없으면 묵시적으로 Object 클래스를 상속
public class Parent {
	public void parentMethod() {
		System.out.println("Parent.parentMethod");
	}
}
```

**클래스에 상속받을 부모 클래스가 없으면 묵시적으로 Object 클래스를 상속받는다.**

```java
public class Child extends Parent{
	public void childMethod() {
		System.out.println("Child.childMethod");
	}
}
```

**클래스에 상속받을 부모 클래스를 명시적으로 지정하면 Object를 상속받지 않는다.**

**이미 상속받는 클래스가 있기에 Java가 extends Object 코드를 넣지 않는다.**

```java
public class ObjectMain {
	public static void main(String[] args) {
		Child child = new Child();
		child.childMethod();
		child.parentMethod();
		
		// toString()은 Obejct 클래스의 메서드
		String string = child.toString();
		System.out.println(string); // object.Child@1f32e575 - 객체에 대한 정보
	}
}
```

**실행 결과**

```java
Child.childMethod
Parent.parentMethod
lang.object.Child@X001
```

1.  **child.toString()을 호출**
2.  **본인 타입인 Child에서 toString()을 찾는다. 없으므로 부모 타입으로 올라가 찾는다.**
3.  **부모 타입인 Parent에서 찾는다. 없으므로 부모 타입으로 올라가 찾는다.**
4.  **부모 타비인 Object에서 찾는다. Object의 toString()이 있으므로 메서드를 호출**

![](/assets/img/posts/305/1.png)

* * *

#### **자바에서 Object 클래스가 최상위 부모 클래스인 이유**

#### **모든 클래스가 Object 클래스를 상속받는 이유**

-   **공통 기능 제공**
-   **다형성의 기본 구현**

**Object는 모든 객체에 필요한 공통기능을 제공한다.**

**Object는 최상위 부모 클래스이기 때문에 모든 객체는** **공통 기능을 편리하게 제공(상속) 받을 수 있다.**

#### **Object가 제공하는 기능**

-   **객체의 정보를 제공하는 toString()**
-   **객체의 같음을 비교하는 equals()**
-   **객체의 클래스 정보를 제공하는 getClass()**
-   **기타 여러 가지 기능**

> **공통 기능 제공**  
>   
> **객체의 정보를 제공하고, 이 객체가 다른 객체와 같은지 비교하고,  
> 객체가 어떤 클래스로 만들어졌는지 확인하는 기능은 모든 객체에게 필요한 기본 기능이다.**  
>   
> **이런 기능을 객체를 만들 때마다 항상 새로운 메서드를 정의해서 만들어야 한다면 상당히 번거로울 것이다.**  
>   
> **그리고 만든다고 해도 개발자마다 서로 다른 이름의 메서드를 만들어서 일관성이 없을 것이다.**

> **다형성의 기본 구현**  
>   
> **부모는 자식을 담을 수 있다. Object는 모든 클래스의 부모 클래스이다. 따라서 모든 객체를 참조할 수 있다.**  
> **Object 클래스는 다형성을 지원하는 기본적인 메커니즘을 제공한다.  
> 모든 Java 객체는 Object 타입으로 처리될 수 있으며, 이는 다양한 타입의 객체를 통합적으로 처리할 수 있게 한다.**  
> **즉, Object는 모든 객체를 담을 수 있다. 타입이 다른 객체들을 어딘가에 보관해야 한다면 바로 Object에 보관하면 된다.**

* * *

#### **Object의 다형성**

-   **Object는 모든 클래스의 부모 클래스이다. 따라서 Object는 모든 객체를 참조할 수 있다**

#### **예시 코드**

```java
public class Car {
	public void move() {
		System.out.println("자동차 이동");
	}
}
```

```java
public class Dog {
	public void sound() {
		System.out.println("멍멍");
	}
}
```

```java
public class ObjectPolyExample {
	public static void main(String[] args) {
		Dog dog = new Dog();
		Car car = new Car();
		
		action(dog);
		action(car);
		
	}

	public static void action(Object obj) {
		// obj.soud // 컴파일 오류, Obejct에는 soud()가 없다.
		// obj.move // 컴파일 오류, Obejct에는 move()가 없다.
		
		// 객체에 맞는 다운캐스팅 필요
		if(obj instanceof Dog dog) {
			dog.sound();
		}else if(obj instanceof Car car) {
			car.move();
		}
	}
}
```

#### **실행결과**

```java
멍멍
자동차 이동
```

> **Object는 모든 타입의 부모다. 부모는 자식을 담을 수 있으므로 위 코드를 다음과 같이 변경 가능하다.**  
>   
> **Object dog = new Dog(); //Dog -> Object**  
> **Object car = new Car();  //Car -> Object**

#### **Object 다형성 장점**

**action(Object obj) 메서드를 분석해 보면, Object 타입의 매개변수를 사용한다**

**그런데 Object는 모든 객체의 부모다. 따라서 어떤 객체든지 인자로 전달할 수 있다**

```java
action(dog);
action(car);
```

#### **Object 다형성의 한계**

-   **Object는 모든 객체를 대상으로 다형적 참조를 할 수 있다**  
    **즉, Obejct는 모든 객체의 부모이므로 모든 객체를 담을 수 있다**
-   **Object를 통해 전달받은 객체를 호출하려면 각 객체에 맞는 다운캐스팅 과정이 필요하다**  
    **Object가 세상의 모든 메서드를 알고 있는 것이 아니다**

**action() 메서드 안에서 obj.sound()를 호출하면 오류가 발생한다. 왜냐하면 매개변수인 obj는 Object 타입이기 때문**

```java
public static void action(Object obj) {
    // obj.soud // 컴파일 오류, Obejct에는 soud()가 없다.
    // obj.move // 컴파일 오류, Obejct에는 move()가 없다.
}
```

**그래서 Dog 인스턴스의 sound()를 호출하려면 다음과 같이 다운캐스팅을 해야 한다.**

```java
// 객체에 맞는 다운캐스팅 필요
if(obj instanceof Dog dog) {
    dog.sound();
}else if(obj instanceof Car car) {
    car.move();
}
```

> **Object는 모든 객체의 부모이므로 모든 객체를 대상으로 다형적 참조를 할 수 있다. 하지만 Object에는 Dog.sound(), Car.move()와 같은 다른 객체의 메서드가 정의되어 있지 않다. 따라서 메서드 오버라이딩을 활용할 수 없다. 결국 각 객체의 기능을 호출하려면 다운캐스팅이 필요하다.**  
>   
> **결과적으로 다형적 참조는 가능하지만, 메서드 오버라이딩이 안되기 때문에 다형성을 활용하기에는 한계가 있다.**
