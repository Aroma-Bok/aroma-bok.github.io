---
title: "[Design Pattern] 생성 패턴 - Abstract Factory"
date: 2023-10-17 10:04:10 +0900
categories: ["Language", "Design Pattern"]
tags: ["factory method vs abstract method", "디자인 패턴 abstract factory", "추상 팩토리 디자인패턴", "추상팩토리 팩토리메소드 차이"]
tistory_url: "https://aroma-bok.tistory.com/entry/Design-Pattern-%EC%83%9D%EC%84%B1-%ED%8C%A8%ED%84%B4-Abstract-Factory"
---
### **Abstract Factory - 추상 팩토리**

-   **추상 팩토리 패턴은 팩토리 메소드 패턴과 Factory 부분은 유사하지만, Factory를 사용하는 Client에게 맞춰져 있다.**
-   **추상 팩토리 패턴의 목적은 팩토리에서 인스턴스를 만들어 사용하는 Client 코드를 인터페이스 기반으로 사용할 수 있도록 도와주도록 하는 것을 목적으로 둔다.**

* * *

#### **팩토리 메소드 (Factory Method) vs** **추상 팩토리 (Abstract Factory)**

#### **팩토리 메소드 (Factory Method)**

**조건에 따른 객체 생성을 팩토리 클래스로 위임하여, 팩토리 클래스에서 객체를 생성하는 패턴**

#### **추상 팩토리 (Abstract Factory)**

**서로 관련이 있는 객체들을 통째로 묶어서 팩토리 클래스로 만들고, 이들 팩토리를 조건에 따라 생성하도록 다시 팩토리를  
만들어서 객체를 생성하는 패턴**

**즉, 추상 팩토리 패턴은 팩토리 메소드를 한번 더 추상 팩토리로 묶은 후, 어떤 클래스를 구현할 것인지 Client 코드에서  
주입해 구체적인 클래스의 의존성을 최대한 제거한 구조라고 볼 수 있다.**

**이렇게 함으로써 어떤 클래스의 인스턴스를 사용할 것인지를 외부에서 호출하는 Client에게 내부를 감출 수 있게 된다.**

**※ 호출하는 쪽은 구현에 신경 쓰지 않고 호출만 하면 된다 → 느슨한 결합이 가능**

> **조금 더 설명을 해보자면**  
>   
> **팩토리 메소드(Factory Method)는 Method이고 추상 팩토리 (Abstract Factory)는 Obejct이다.**  
> **즉, 팩토리 메소드(Factory Method)는 구체적인 값을 얻을 수 있도록 SubClass에 위임해 값을 얻고,  
> ****추상 팩토리 (Abstract Factory )는 그러한 다양한 팩토리 메소드(Factory Method)들을 가지고 있는 Object이다.**  
>   

**공통점**

-   **둘 다 구체적인 객체 생성 과정을 추상화한 인터페이스를 제공**

**차이점**

-   **팩토리 메소드 패턴은 팩토리를 구현하는 방법 (Inheritance)에 초점**
-   **추상 팩토리 패턴은 팩토리를 사용하는 방법 (Composition)에 초점**
-   **팩토리 메소드 패턴은 구체적인 객체 생성 과정을 하위 또는 구체적인 클래스로 옮기는 것이 목적**
-   **추상 팩토리 패턴은 관련 있는 여러 객체를 구체적인 클래스에 의존하지 않고 만들 수 있게 해주는 것이 목적**

* * *

#### **추상 팩토리 (Abstract Factory) 구현**

-   **Client 코드에서 구체적인 클래스의 의존성을 제거한다**

-   **Ship을 구성하는 요소들**

```java
@Setter
@Getter
@ToString
public class Ship {

    private String name;
    private String email;
    private String color;

    private Anchore anchor;
    private Wheel wheel;

    public void setAnchor(Anchore anchor) {
        this.anchor = anchor;
    }

    public void setWheel(Wheel wheel) {
        this.wheel = wheel;
    }
}
```

-   **Wheel과 Anchore는 어떠한 값이 들어올지 모르니, Interface로 개발**

```java
public interface Wheel {
}

public interface Anchore {
}
```

-   **어떤 부품을 구현할 것인지는 팩토리 메소드 패턴과 비슷하게 하위 구현체가 선택**

```java
public interface ShipPartsFactory {
    Anchore createAnchor();

    Wheel createWheel();
}

-----------------------------------

public class WhiteShipPartsFactory implements ShipPartsFactory{
    @Override
    public Anchore createAnchor() {
        return new WhiteAnchor();
    }

    @Override
    public Wheel createWheel() {
        return new WhiteWheel();
    }
}
```

-   **다른 타입의 Parts를 가진 Factory 생성**

```java
public class WhiteAnchorPro implements Anchore{
}
public class WhiteWheelPro implements Wheel{
}

-----------------------------------------------

public class WhitePartsProFactory implements ShipPartsFactory{
    @Override
    public Anchore createAnchor() {
        return new WhiteAnchorPro();
    }

    @Override
    public Wheel createWheel() {
        return new WhiteWheelPro();
    }
}
```

-   **구체적으로 ShipFactory를 구현하는 하위 클래스에서 어떤 부품 객체를 구현할지 외부에서 주입받을 수 있도록  
    미리 구현해 준 Interface를 생성자로 주입받는다.**

```java
public interface ShipFactory {
    default Ship orderShip(String name, String email) {
        validate(name, email);
        return createShip(name, email);
    }
    Ship createShip(String name, String email);

    private void validate(String name, String email) {
        if(Objects.isNull(name) || name.isBlank()) {
            throw new IllegalArgumentException("배 이름을 지어주세요");
        }

        if(Objects.isNull(email) || email.isBlank()) {
            throw new IllegalArgumentException("연락처를 남겨주세요");
        }
    }
}
----------------------------------------------------------

public class WhiteShipFactory implements ShipFactory{
    private ShipPartsFactory shipPartsFactory;
    
    public WhiteShipFactory(ShipPartsFactory shipPartsFactory) {
        this.shipPartsFactory = shipPartsFactory;
    }
    
    @Override
    public Ship createShip(String name, String email) {
        Ship ship = new WhiteShip(name, email);
        ship.setAnchor(shipPartsFactory.createAnchor());
        ship.setWheel(shipPartsFactory.createWheel());
        return ship;
    }
}
```

-   **결과적으로, 클라이언트 쪽에서 어떤 구현을 원하는지 주입해주기만 하면 되기에 팩토리 메소드 패턴과**  
    **마찬가지로 요구사항이 변경되어도 내부 코드는 변경되지 않고 확장할 수 있는 구조가 된다.**

```java
public class AbstractFactoryTest {
    @Test
    void ShipInventoryTest() {
        ShipFactory whiteShipFactory = new WhiteShipFactory(new WhiteShipPartsFactory());
        Ship ship = whiteShipFactory.createShip("추상팩토리 배", "추상팩토리1");

        System.out.println("ship.getAnchor().getClass() = " + ship.getAnchor().getClass());
        System.out.println("ship.getWheel().getClass() = " + ship.getWheel().getClass());
        //ship.getAnchor().getClass() = class AbstractFactory.WhiteAnchor
        //ship.getWheel().getClass() = class AbstractFactory.WhiteWheel

        ShipFactory whiteShipFactoryPro = new WhiteShipFactory(new WhitePartsProFactory());
        ship = whiteShipFactoryPro.createShip("추상팩토리프로 배", "추상팩토리2");

        System.out.println("ship.getAnchor().getClass() = " + ship.getAnchor().getClass());
        System.out.println("ship.getWheel().getClass() = " + ship.getWheel().getClass());
        //ship.getAnchor().getClass() = class AbstractFactory.WhiteAnchorPro
        //ship.getWheel().getClass() = class AbstractFactory.WhiteWheelPro
    }
}
```

* * *

**추상 팩토리 (Abstract Factory) 정리**

-   **팩토리 메소드 패턴 (Factory Method)**  
    **\- Factory를 구현하는 방법(Inheritance)에 초점**  
    **\- 구체적인 객체 생성 과정을 하위 또는 구체적인 클래스로 옮기는 것이 목적  
      
    **
-   **추상 팩토리 패턴 (Abstract Factory)  
    ****\- 팩토리를 사용하는 (Composition) 방법에 초점**  
    **\- 관련 있는 여러 객체를 구체적인 클래스에 의존하지 않고 만들 수 있게 해주는 것이 목적**  
    **\- Product를 생산하는 Factory를 인터페이스로 묶고 → Client에서 구체적인 클래스가 아닌 인터페이스에 의존**
