---
title: "[Java] Object 클래스에 대한 개념 정리-2"
date: 2025-02-04 13:24:58 +0900
categories: ["Language", "Java"]
tags: ["java object", "김영한 object", "김영한 자바 중급", "자바 object", "자바 오브젝트"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-Object-%ED%81%B4%EB%9E%98%EC%8A%A4%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B0%9C%EB%85%90-%EC%A0%95%EB%A6%AC-2"
---
#### **Object 배열**

-   **Object는 모든 타입의 객체를 담을 수 있다. 따라서 Object \[\]을 만들면 세상의 모든 객체를 담을 수 있는 배열이다**

```java
public class ObjectPolyExample2 {
	public static void main(String[] args) {
		Dog dog = new Dog();
		Car car = new Car();
		
		Object object = new Object(); // Object 인스선스
		
		Object[] objects = {dog, car, object};
		
		size(objects);
	}
	private static void size(Object[] objects) {
		System.out.println("전달된 객체의 수는 :" + objects.length);
	}
}
```

**실행결과**

```java
전달된 객체의 수는: 3
```

**Object 타입을 사용한 덕분에 세상의 모든 객체를 담을 수 있는 배열을 만들 수 있다.**

![](/assets/img/posts/306/1.png)

> **size() 메서드  
> **  
> **size(Object \[\] objects) 메서드는 배열에 담긴 객체의 수를 세는 역할을 담당**  
> **이 메서드는 Object 타입만 사용한다. Object 타입의 배열은 세상의 모든 객체를 담을 수 있기 때문에,**  
> **새로운 클래스가 추가되거나 변경되어도 이 메서드를 수정하지 않아도 된다.  
> 지금 만든 size() 메서드는** **Java를 사용하는 곳이라면 어디든지 사용 가능하다.**

#### **Object가 없다면?**

-   **void action\*Object obj)과 같이 모든 객체를 받을 수 있는 메서드를 만들 수 없다**
-   **Object \[\] objects처럼 모든 객체를 저장할 수 있는 배열을 만들 수 없다**

> **Object가 없어도 직접 비슷한 클래스를 만들고 모든 클래스에서 직접 정의한 클래스를 상속받으면 된다.**  
> **하지만 하나의 프로젝트를 넘어서 전 세계 개발자가 비슷한 클래스를 만들 것이고,  
> 서로 호환되지 않는 수많은 비슷한 클래스가 넘쳐날 것이다.**

* * *

#### **toString()**

-   **Object.toString() 메서드는 객체의 정보를 문자열 형태로 제공**
-   **디버깅과 로깅에 유용하게 사용**
-   **Object 클래스에 정의되므로 모든 클래스에서 상속받아 사용 가능**

```java
public class ToStringMain1 {
	public static void main(String[] args) {
		Object object = new Object();
		String string = object.toString();
		
		// toString()반환값 출력
		System.out.println(string);

		// object 직접 출력
		System.out.println(object);
	}
}
```

#### **실행결과**

```java
java.lang.Object@a09ee92
java.lang.Object@a09ee92
```

#### **Object.toString()**

-   **Object가 제공하는 toString() 메서드는 기본적으로 패키지를 포함한 객체의 이름과 객체의 참조값(해시코드)을  
    16진수로 제공**

```java
public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```

#### **println()과 toString()**

-   **toString()의 결과를 출력한 코드와 object를 println()에 직접 출력한 코드의 결과가 같다**
-   **System.out.println() 메서드는 사실 내부에서 toString()을 호출**
-   **println()을 사용할 때, toString()을 직접 호출할 필요 없이 객체를 바로 전달하면 객체의 정보를 출력 가능**

```java
Object object = new Object();
String string = object.toString();

// toString()반환값 출력
System.out.println(string);

// object 직접 출력
System.out.println(object);
```

* * *

#### **toString() 오버라이딩**

-   **Object.toString() 메서드가 클래스 정보와 참조값을 제공하지만 이 정보만으로는 객체의 상태를 적절히 나타내지 못함**
-   **보통 toString()을 재정의(오버라이딩)해서 보다 유용한 정보를 제공하는 것이 일반적**

#### **예시 코드**

**Car 인스턴스는 toString()을 재정의 하지 않았다. 따라서 Object가 제공하는 기본 toString() 메서드를 사용**

```java
public class Car {
	private String carName;
	
	public Car(String string) {
		this.carName = carName;
	}
}
```

**Dog 인스턴스는 toString()을 재정의 한 덕분에 객체의 상태를 명확하게 확인 가능**

```java
public class Dog {
	private String dogName;
	private int age;
	
	public Dog(String dogName, int age) {
		this.dogName = dogName;
		this.age = age;
	}
	 
	@Override
	public String toString() {
		return "dogNAme=" + dogName + " age=" +age;
	}
}
```

```java
public class ObjectPriter {
	public static void print(Object obj) {
		String string = "객체 정보 출력:" + obj.toString();
		System.out.println(string);
	}
}
```

```java
public class ToStringMain2 {
	public static void main(String[] args) {
		Car car = new Car("Model Y");
		Dog dog1 = new Dog("멍멍1", 2);
		Dog dog2 = new Dog("멍멍2", 5);
		
		System.out.println("1. 단순 toString 호출");
		System.out.println(car.toString());
		System.out.println(dog1.toString());
		System.out.println(dog2.toString());
		
		System.out.println("2. println 내부에서 toStirng 호출");
		System.out.println(car);
		System.out.println(dog1);
		System.out.println(dog2);
		
		System.out.println("3. Object 다형성 활용");
		ObjectPriter.print(car);
		ObjectPriter.print(dog1);
		ObjectPriter.print(dog2);
		
		//참조값 확인
		int i = System.identityHashCode(dog1);
		System.out.println(i);
		
		// 16진수로 변경
		String string = Integer.toHexString(System.identityHashCode(dog1));
		System.out.println(string);
	}
}
```

#### **실행결과**

```java
1. 단순 toString 호출
lang.object.tostring.Car@452b3a41
Dog{dogName='멍멍이1', age=2}
Dog{dogName='멍멍이2', age=5}

2. println 내부에서 toString 호출
lang.object.tostring.Car@452b3a41
Dog{dogName='멍멍이1', age=2}
Dog{dogName='멍멍이2', age=5}

3. Object 다형성 활용
객체 정보 출력: lang.object.tostring.Car@452b3a41
객체 정보 출력: Dog{dogName='멍멍이1', age=2}
객체 정보 출력: Dog{dogName='멍멍이2', age=5}
```

![](/assets/img/posts/306/2.png)

> **참고**  
>   
> **toString()은 기본으로 객체의 참조값을 출력한다. 그런데 toString()이나 hashCode()를 재정의하면 객체의 참조값을 출력할 수 없다. 이때는 다음 코드를 사용하면 객체의 참조값을 출력할 수 있다.**

```java
//참조값 확인
int i = System.identityHashCode(dog1);
System.out.println(i);

// 16진수로 변경
String string = Integer.toHexString(System.identityHashCode(dog1));
System.out.println(string);
```

#### **실행결과**

```java
 refValue = 72ea2f77
```

* * *

#### **Object와 OCP**

**만약 Object가 없고, 또 Object가 제공하는 toString()이 없다면 서로 아무 관계가 없는 객체의 정보를 출력하기 어려울 것이다. 여기서 아무 관계없다는 것은 공통의 부모가 없다는 뜻이다. 아마도 다음의 클래스와 같이 각각의 클래스마다 별도의 메서드를 작성해야 할 것이다.**

```java
public class BadObjectPrinter {
    public static void print(Car car) { //Car 전용 메서드
        String string = "객체 정보 출력: " + car.carInfo(); //carInfo() 메서드 만듬
        System.out.println(string);
    }
    public static void print(Dog dog) { //Dog 전용 메서드
        String string = "객체 정보 출력: " + dog.dogInfo(); //dogInfo() 메서드 만듬
        System.out.println(string);
    }
}
```

> **구체적인 것에 의존**  
>   
> **BadObjectPrinter는 구체적인 타입인 Car, Dog를 사용한다. 따라서 이후에 출력해야 할 구체적인 클래스가 10개로 늘어나면 구체적인 클래스에 맞추어 메서드도 10개로 늘어나게 된다. 이렇게 BadObjectPrinter 클래스가 구체적인 특정 클래스인 Car, Dog를 사용하는 것을 BadObjectPrinter는 Car, Dog에 의존한다고 표현한다.**  
>   
> **다행히도 Java에는 객체의 정보를 사용할 때, 다형적 참조 문제를 해결해 줄 Object 클래스와 메서드 오버라이딩 문제를 해결해 줄 Object.toString() 메서드가 있다.**

> **추상적인 것에 의존**  
>   
> **우리가 앞서 만든 ObjectPrinter 클래스는 Car, Dog 같은 구체적인 클래스를 사용하는 것이 아니라, 추상적인 Object 클래스를 사용한다. 이렇게 ObjectPrinter 클래스가 Object 클래스를 사용하는 것을**  
> **ObjectPrinter 클래스가 Object에 클래스에 의존한다고 표현한다.**

```java
public class ObjectPriter {
	public static void print(Object obj) {
		String string = "객체 정보 출력:" + obj.toString();
		System.out.println(string);
	}
}
```

#### **ObjectPriter의 print() 메서드와 전체 구조 분석**

-   **다형적 참조**  
    **print(Object obj), Object 타입을 매개변수로 사용해서 다형적 참조를 사용**  
    **Car, Dog 인스턴스를 포함한 세상의 모든 객체 인스턴스를 인수로 받을 수 있다**
-   **메서드 오버라이딩**  
    **Object는 모든 클래스의 부모이다. 따라서 Dog, Car와 같은 구체적인 클래스는 Object가 가지고 있는 toString() 메서드를 오버라이딩 할 수 있다. 따라서 print(Object obj) 메서드는 Dog, Car와 같은 구체적인 타입에 의존(사용) 하지 않고, 추상적인 Object 타입에 의존하면서 런타임에 각 인스턴스의 toString()을 호출할 수 있다**

> **OCP 원칙**  
>   
> **Open : 새로운 클래스를 추가하고, toString()을 오버라이딩해서 기능을 확장할 수 있다.**  
> **Closed : 새로운 클래스를 추가해도 Object와 toString()을 사용하는 클라이언트 코드인 ObejctPrinter는 변경하지 않아도 된다.**

**다형적 참조, 메서드 오버라이딩, 그리고 클라이언트 코드가 구체적인 Car, Dog에 의존하는 것이 아니라 추상적인 Object에 의존하면서 OCP 원칙을 지킬 수 있었다. 덕분에 새로운 클래스를 추가하고 toString() 메서드를 새롭게 오버라이딩해서 기능을 확장할 수 있다. 그리고 이러한 변화에도 불구하고 클라이언트 코드인 ObjectPriter는 변경할 필요가 없다.**

**ObjectPriter는 Object 타입을 사용하고 Object가 제공하는 toString() 메서드만 사용한다.**

**따라서 ObjectPriter를 사용하면 세상의 모든 객체의 정보를 편리하게 출력할 수 있다.**
