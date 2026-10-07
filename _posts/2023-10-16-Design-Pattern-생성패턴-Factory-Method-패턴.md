---
title: "[Design Pattern] 생성패턴 - Factory Method 패턴"
date: 2023-10-16 12:50:04 +0900
categories: ["Language", "Design Pattern"]
tags: ["factory method 패턴", "디자인 패턴 factory", "디자인패턴 팩토리메소드", "팩토리 메서드"]
tistory_url: "https://aroma-bok.tistory.com/entry/Design-Pattern-%EC%83%9D%EC%84%B1%ED%8C%A8%ED%84%B4-Factory-Method-%ED%8C%A8%ED%84%B4"
---
#### **팩토리 메소드(Factory Method) 패턴**

-   **구체적으로 어떤 인스턴스를 만들지를 서브클래스가 결정하는 패턴**
-   **상위 클래스는 객체 생성을 위한 인터페이스를 제공, 하위 클래스에게 구현체 클래스의 객체 생성일 위임하는 패턴**
-   **팩토리 메소드 패턴은, 기능 혹은 역할에 따라 다양한 구현체(Product)가 존재하고, 그중에서 특정한 구현체를 만들 수 있는 다양한 팩토리(Creator)를 제공할 수 있다.**

#### **팩토리 메소드 (Factory Method) 사용 이유**

-   **생성할 객체 타입을 예측할 수 없을 때**
-   **생성할 객체를 기술하는 책임을 서브 클래스에 정의하고자 할 때**
-   **객체 생성의 책임을 서브 클래스에 위임시키고 서브클래스에 대한 정보를 은닉하고자 할 때**

**장점**

-   **기존 코드(인스턴스를 만드는 과정)를 수정하지 않고 새로운 인스턴스를 다른 방법으로 생성하도록 확장 가능**
-   **Product와 Creator 간의 결합도(Coupling)이 느슨함**
-    **확장에는 열려있고 변경에는 닫혀있는 객체지향 원칙을 적용했기에 가능**  
    **★ 확장 : 새로운 인스턴스를 추가**  
    **★ 변경 : 기존 코드를 수정**
-   **코드가 간결해짐**
-   **병렬적 클래스 계층도를 연결하는 역할을 할 수 있음**

**단점**

-   **클래스가 많아진다.** 
-   **클래스 계층도 커질 수 있다.**  
    **\- 제품 클래스가 바뀔 때마다 새로운 서브 클래스를 생성해야 한다.**
-   **클라이언트가 Creator 클래스를 반드시 상속해 Product를 생성해야 한다.**

#### **팩토리 메소드 (Factory Method) 구현방법**

-   **팩토리 메소드의 목적은 "변경에는 닫혀있고, 확장에는 열려있는 구조"를 만드는 것이다.  
    OCP (Open Closed Principle) 원칙을 지키기 위한 패턴에 가깝다.**

 **좋지 않은 코드 예시**

```java
@Setter
@ToString
public class Ship {

    private String name;
    private String email;
    private String color;

}
```

```java
public class ShipFactory {
    public static Ship orderShip(final String name, final String email) {

        if(Objects.isNull(name) || name.isEmpty()) {
            throw new IllegalArgumentException("배 이름을 지어주세요");
        }
        if(Objects.isNull(name) || email.isEmpty()) {
            throw new IllegalArgumentException("연락처를 남겨주세요");
        }

        Ship ship = new Ship();
        ship.setName(name);

        if(name.equalsIgnoreCase("whiteship")) {
            ship.setColor("white");
        }
        if(name.equalsIgnoreCase("blackship")) {
            ship.setColor("black");
        }

        return ship;
    }
}
```

```java
public class orderShipTest {

    @Test
    void orderShipTest() {
        Ship whiteShip = ShipFactory.orderShip("WhiteShip", "abc@email.com");
        System.out.println(whiteShip);

        Ship blackShip = ShipFactory.orderShip("BlackShip", "def@email.com");
        System.out.println(blackShip);
    }
}
```

> **해당 코드의 문제점**  
>   
> **Factory를 통해 요구조건에 따라 다른 Ship 객체가 반환되지만, 만약 요구사항이 달라진다면 Factory 코드도 변경되어야 한다. 즉, 변경에 닫혀있지 않은 코드이다.**

* * *

**좋은 코드 예시**

-   **기존 ShipFactory에서 진행하던 validate도 ShipFactory로 하며, 배를 직접적으로 생성시키는 createShip() 메소드만 추상 메소드로 정의**

```java
public interface ShipFactoryInterface {
    default Ship orderShip(String name, String email) {
        validate(name, email);
        return createShip(name, email);
    }

    Ship createShip(String name, String email);

    private void validate(String name, String email) {
        if(Objects.isNull(name) || name.isEmpty()) {
            throw new IllegalArgumentException("배 이름을 지어주세요");
        }

        if(Objects.isNull(email) || email.isEmpty()) {
            throw new IllegalArgumentException("연락처를 남겨주세요");
        }
    }
}
```

-   **직접 배를 만들어 반환하는 createShip() 메서드를 구현하는 구현체를 각각 책임에 맞게 정의**

```java
public class WhiteShipFactory implements ShipFactoryInterface{
    @Override
    public Ship createShip(String name, String email) {
        return new WhiteShip(name, email);
    }
}

-----------------------------------------------------------

public class WhiteShip extends Ship{
    public WhiteShip(String name, String email) {
        setName(name);
        setColor("White");
        setEmail(email);
    }
}
```

```java
public class BlackShipFactory implements ShipFactoryInterface{
    @Override
    public Ship createShip(String name, String email) {
        return new BlackShip(name, email);
    }
}
--------------------------------------------------------------

public class BlackShip extends Ship{
    public BlackShip(String name, String email) {
        setName(name);
        setColor("Black");
        setEmail(email);
    }
}
```

-   **생성할 배에 따라서, 해당하는 구현체를 선택**

```java
    @Test
    void orderShipInterfaceTest() {
        Ship whiteShip = new WhiteShipFactory().orderShip("WhiteShip1", "abc@email.cmo");
        System.out.println(whiteShip);

        Ship blackShip = new BlackShipFactory().orderShip("BlackShip1", "def@email.cmo");
        System.out.println(blackShip);
    }
```

> **중요**  
> **WhiteShip, BlackShip 등 요구사항 변경에 의해 추가적인 배 객체를 만들어야 해도,**  
> **기존 코드를 수정하는 것이 아니라,  
> createShip() 메소드를 구현하는 구현체만 추가해 주면 된다.  
> **  
> **즉, 변경에는 닫혀있고 확장에는 열려있는 OCP를 지키는 구조가 된다.**

**※ Java8 default Method에 대해서 잘 모른다면, 아래 포스팅 참조 **※****

**[2023.10.16 - \[Language/Java\] - \[Java\] Java8 default Method 개념 정리](https://aroma-bok.tistory.com/entry/Java-Java8-default-Method-%EA%B0%9C%EB%85%90-%EC%A0%95%EB%A6%AC)**

* * *

#### **팩토리 메소드 패턴 복습**

**장점**

-   **요구사항의 변경에도 기존코드를 변경하지 않고 확장하여 처리할 수 있는 구조가 된다.**
-   **Product와 Creator 간 관계를 느슨한 결합으로 묶었기 때문에 가능한 것이다.**

**단점**

-   **각자의 역할을 가지고 있는 클래스가 늘어남은 막을 수 없다.**  
    **즉, 관리포인트가 증가하게 된다.**

**OCP (Open Closed Principle)**

-   **변경에 닫혀있다 : 기존 코드를 변경하지 않는다**
-   **확장에 열려있다 : 새로운 코드를 확장한다**

**default 메소드**

-   **Java8에 추가됨**
-   **Java8 이전에는 interface는 추상 메소드만 선언 가능했었지만, 이후로는 default 메소드를 정의할 수 있기 때문에**  
    **추상 메소드를 상속받는 모든 구현체에서 필요한 기능을 상속받을 수 있도록 되었다.**
