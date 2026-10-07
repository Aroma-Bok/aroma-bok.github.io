---
title: "[Design Pattern] 행동패턴 - Visitor"
date: 2023-11-17 12:39:34 +0900
categories: ["Language", "Design Pattern"]
tags: ["designpattern java visitor", "designpattern visitor", "java visitor", "디자인패턴 visitor", "디자인패턴 방문자", "자바 방문자 패턴"]
tistory_url: "https://aroma-bok.tistory.com/entry/Design-Pattern-%ED%96%89%EB%8F%99%ED%8C%A8%ED%84%B4-Visitor"
---
### **방문자 (Visitor) 패턴**

-   **기존 코드를 건드리지 않고 새로운 코드를 추가하는 방법을 제안하는 패턴**
-   **방문자 패턴은 비즈니스 로직을 객체 구조에서 분리시키는 디자인 패턴**
-   **비슷한 종류의 객체들을 가진 그룹에서 작업을 수행해야 할 때 주로 사용**
-   **더블 디스패치 (Double Dispatch)를 활용**

> **디스패치(Dispatch)란?**  
> **자바는 객체지향 프로그래밍 언어로써 객체들 간의 메시지 전송을 기반으로 문제를 해결한다.**  
> **그리고 메세지 전송이라는 표현은 결국 메소드를 호출하는 것인데, 그것을 Dispatch라고 한다.**  
>   
> **자바는 하위타입으로의 묵시적 형변환을 지원하지 않는, 싱글 디스패치(Single Dispatch) 언어이다.**  
> **따라서 런타임시에 부모 객체의 구현체로 어떤 자식 클래스가 들어오는지 확인하여 서로 다른 메소드를**  
> **호출해 주는 동적 디스패치를 지원하지 않는다.**  
>   
> **이러한 언어의 한계적 특성으로 인해 인자에 동적 디스패치를 활용하지 못하기 때문에, 이를 위해 한번 더**  
> **다형성을 이용함으로써 호출 객체 동적 디스패치를 이용하는 기법이 더블 디스패치이다.**

#### **구조**

-   **Client : 명령을 보낸다**
-   **Visitor : 명령을 수행하기 위해 필요한 메소드를 정의하는 인터페이스**
-   **ConcreteVisitor : 명령을 수행하는 메소드를 구현**
-   **Element : visit을 사용할 수 있는지 확인하는 accept 메소드를 정의하는 인터페이스**
-   **ConcreteElement : Visitable에서 정의된 accept 메소드를 구현하며 Visitor 객체는 이 객체를 통해 명령을 전달**

![](/assets/img/posts/249/1.png)

* * *

#### **방문자 (Visitor) 패턴 적용 전**

-   **인터페이스 B를 인자로 받는 인터페이스 A가 존재**
-   **인터페이스 A의 구현체에서 인자로 받은 인터페이스 B의 구현체 타입에 따라 다른 행위를 해야 하는 요구조건**
-   **방문자(Visitor) 패턴을 적용하지 않고, '분기처리'로 해당 매개변수로 들어온 구현체의 클래스를 판단**

-   **Shape 인터페이스를 상속받는 Rectangle, Triangle 구현체에게 들어오는 Device 인터페이스의 구현체에 따라**
-   **각각 분기처리**
-   **이렇게 하면, Shape 혹은 Device의 구현체가 늘어나거나 특정 상황에서의 행위가 달라지면 매번 수정해야 하는**
-   **결합도가 놓은 상태가 된다.**

```java
public interface Shape {
    void printTo(Device device);
}
public class Rectangle implements Shape {
    // Device의 구현체가 추가되더라도 여기 코드가 변경됨
    @Override
    public void printTo(Device device) {
        if(device instanceof Phone) {
            System.out.println("print Rectangle to Phone");
        }
        if(device instanceof Watch) {
            System.out.println("print Rectangle to Watch");
        }
    }
}
public class Triangle implements Shape {
    // Device의 구현체가 추가되더라도 여기 코드가 변경됨
    @Override
    public void printTo(Device device) {
        if(device instanceof Phone) {
            System.out.println("print Triangle to Phone");
        }
        if(device instanceof Watch) {
            System.out.println("print Triangle to Watch");
        }
    }
}
```

```java
public interface Device {
}
public class Phone implements Device{
}
public class Watch implements Device {
}
```

```java
    @Test
    void test1() {
        Shape rectangle = new Rectangle();
        Shape triangle = new Triangle();

        Device phone = new Phone();
        Device watch = new Watch();

        rectangle.printTo(watch);
        rectangle.printTo(phone);

        triangle.printTo(watch);
        triangle.printTo(phone);
    }
```

* * *

#### **방문자 (Visitor) 패턴 적용 후**

-   **방문자 (Visitor) 패턴을 이용하여 해당 코드의 의존성을 제거해 보도록 하겠습니다.**
-   **방문자 패턴의 핵심은 더블 디스패치. 기존의 Shape 구현체에서 일어나던 비즈니스 로직을 Device에게 직접 처리하도록 수정**

-   **Client에서 accept() 메소드를 찾기위해 1차 Dispatch(정적 디스패치)가 발생**
-   **print() 객체를 호출하기 위해 메소드를 호출하는 인자가 구현체가 없는 인터페이스 타입인 경우, 런타임시에 객체를 찾아 알맞은 메소드를 호출하는 2차 Dispatch(동적 디스패치)가 발생**
-   **1차 (정적) + 2차 (동적) = 총 2번의 디스패치 발생**

```java
public interface ShapeAfter {
    // Visitor로 Dispatch하기 위한 기능
    void accept(DeviceAfter device);
}
public class RectangleAfter implements ShapeAfter {
    @Override
    public void accept(DeviceAfter device) {
        device.print(this);
    }
}
public class TriangleAfter implements ShapeAfter {
    @Override
    public void accept(DeviceAfter device) {
        device.print(this);
    }
}
```

-   **필요한 기능이 생기면 변경하지 않고 추가하면 된다**

```java
public interface DeviceAfter {
    void print(CircleAfter circle);
    void print(TriangleAfter triangle);
    void print(RectangleAfter rectangle);
}
public class PhoneAfter implements DeviceAfter{
    @Override
    public void print(CircleAfter circle) {
        System.out.println("print Circle to Phone");
    }

    @Override
    public void print(TriangleAfter triangle) {
        System.out.println("print Triangle to Phone");
    }

    @Override
    public void print(RectangleAfter rectangle) {
        System.out.println("print Rectangle to Phone");
    }
}
public class WatchAfter implements DeviceAfter {
    @Override
    public void print(CircleAfter circle) {
        System.out.println("print Circle to Watch");
    }

    @Override
    public void print(TriangleAfter triangle) {
        System.out.println("print Triangle to Watch");
    }

    @Override
    public void print(RectangleAfter rectangle) {
        System.out.println("print Rectangle to Watch");
    }
}
```

-   **호출하는 클라이언트 코드는 같다.**

```java
    @Test
    void test2() {
        ShapeAfter rectangle = new RectangleAfter();
        ShapeAfter circle = new CircleAfter();
        ShapeAfter triangle = new TriangleAfter();

        DeviceAfter phone = new PhoneAfter();
        DeviceAfter watch = new WatchAfter();

        // 상위 타입인 device가 지원이 되지 않음
        rectangle.accept(phone);
        rectangle.accept(watch);

        circle.accept(phone);
        circle.accept(watch);

        triangle.accept(phone);
        triangle.accept(watch);
    }
```

* * *

#### **방문자 (Visitor) 패턴**

#### **장점**

-   **기존 코드를 수정하지 않고 새로운 기능을 추가할 수 있다 (1차 Dispatch 기능은 추가해야 함)**
-   **추가 기능을 한 곳에 모아둘 수 있다.**

#### **단점**

-   **새로운 Element를 추가하거나 제거할 때 모든 Visitor 코드를 변경해야 한다.**
-   **visit 메소드의 return 타입을 각각 파악하고 있어야 한다.**
