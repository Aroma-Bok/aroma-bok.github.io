---
title: "[Design Pattern] 행동패턴 - Strategy"
date: 2023-11-16 11:39:04 +0900
categories: ["Language", "Design Pattern"]
tags: ["design pattern strategy", "design pattern strategy java", "java strategy", "디자인패턴 strategy", "디자인패턴 전략", "디자인패턴 전략패턴", "자바 전략패턴"]
tistory_url: "https://aroma-bok.tistory.com/entry/Design-Pattern-%ED%96%89%EB%8F%99%ED%8C%A8%ED%84%B4-Strategy"
---
### **전략 (Strategy) 패턴**

-   **여러 알고리즘을 캡슐화하고 상호 교환 가능하게 만드는 패턴**
-   **실행(런타임) 중에 알고리즘 전략을 선택하여 객체 동작을 실시간으로 바뀌도록 할 수 있게 하는 패턴**
-   **'전략'이란 일종의 알고리즘이나 기능, 동작이 될 수도 있는 특정판 목표를 수행하기 위한 행동 계획이다.**
-   **어떤 일을 수행하는 알고리즘이 여러 가지 일 때, 동작들을 미리 전략으로 정의함으로써 손쉽게 전략을 교체할 수 있는 알고리즘 변형이 빈번하게 필요한 경우 적합한 패턴**

#### **구조**

-   **전략 알고리즘 객체들 (Concreate Strategy) : 알고리즘, 행위, 동작을 객체로 정의한 구현체**
-   **전략 인터페이스 : 모든 전략 구현체에 대한 공용 인터페이스**
-   **컨텍스트 (Context) : 알고리즘을 실행해야 할 때마다 해당 알고리즘과 연결된 전략 객체의 메소드를 호출**
-   **클라이언트 : 특정 전략 객체를 컨텍스트에 전달함으로써 전략을 등록하거나 변경하여 전략 알고리즘을 실행한 결과를 누린다.**

![](/assets/img/posts/248/1.png)

* * *

#### **전략 (Strategy) 패턴 사용 시기**

-   **전략 알고리즘의 여러 버전 또는 변형이 필요할 때 클래스화를 통해 관리**
-   **알고리즘 코드가 노출되어서는 안 되는 데이터에 접근하거나 데이터를 활용할 때 (캡슐화)**
-   **알고리즘의 동작이 런타임에 실시간으로 교체되어야 할 때**

* * *

#### **전략 (Strategy) 패턴 적용 전**

-   **RPG 게임에서의 무기 전략**

-   **state 매개변수의 값에 따라서 간접적으로 attack() 동작을 제어하도록 되어있다.**
-   **상수를 메소드에 넘겨 조건문으로 일일이 필터링 처리**

```java
public class TakeWeapon {
    public static final int SWORD = 0;
    public static final int SHIELD = 1;
    public static final int CROSSBOW = 2;

    private int state;

    void setWeapon(int state) {
        this.state = state;
    }

    void attack() {
        if(state == SWORD) {
            System.out.println("칼을 휘두르다.");
        }else if(state == SHIELD) {
            System.out.println("방패로 밀친다.");
        }else if(state == CROSSBOW) {
            System.out.println("활을 쏜다.");
        }
    }
}
```

   
 

```java
    @Test
    void test1() {

        //무기 착용 전략 설정
        TakeWeapon hand = new TakeWeapon();

        // 검을 들도록 전략 설정
        hand.setWeapon(TakeWeapon.SWORD);
        hand.attack();

        // 방패를 들도록 전략 설정
        hand.setWeapon(TakeWeapon.SHIELD);
        hand.attack();
    }
```

   
   
**상태 변수를 통해 행위를 분기문으로 나누는 행위는 좋지 않은 코드이다. 잘못하면 if - else 지옥에 빠질 수 있기 때문이다.**

* * *

#### **전략 (Strategy) 패턴 적용 후**

-   **위의 클린 하지 않은 코드를 해결하는 가장 좋은 방법은 변경시키고자 하는 행위(전략)를 직접 넘겨주는 것**

-   **여러 무기들을 객체 구현체로 정의하고 이들을 Weapon이라는 인터페이스로 묶어 준다.**
-   **그리고 인터페이스를 컨텍스트 클래스에 합성(Composition) 시키고, setWeapon() 메소드를 통해 전략 인터페이스  
    객체의 상태를 바로바로 변경할 수 있도록 구성**

```java
public interface Weapon {
    void offensive();
}
```

```java
public class Sword implements Weapon {
    @Override
    public void offensive() {
        System.out.println("칼을 휘두르다");
    }
}
```

```java
public class Shield implements Weapon {
    @Override
    public void offensive() {
        System.out.println("방패로 밀친다");
    }
}
```

```java
public class CrossBow implements Weapon {
    @Override
    public void offensive() {
        System.out.println("화살을 발사하다.");
    }
}
```

-   **Context - 전략을 등록하고 실행**

```java
public class TakeWeaponStrategy {
    Weapon weapon;

    void setWeapon(Weapon weapon) {
        this.weapon = weapon;
    }

    void attack() {
        weapon.offensive();
    }
}
```

-   **Client - 전략 제공/설정**

```java
    @Test
    void test2() {
        // 무기착용 전략
        TakeWeaponStrategy hand = new TakeWeaponStrategy();

        // 검을 들도록 전략 설정
        hand.setWeapon(new Sword());
        hand.attack();

        // 방패를 들도록 전략 설정
        hand.setWeapon(new Shield());
        hand.attack();

        // 화살을 들도록 전략 설정
        hand.setWeapon(new CrossBow());
        hand.attack();
    }
```

   
   
**전략 패턴 적용 전에는 메소드에 상수값을 넘겨주었지만,**  
**전략 패턴에서는 인스턴스를 넣어 알고리즘을 수행하도록 한 것이다.**  
   
**이런 식으로 구성하면 새로운 무기를 추가한다고 했을 때, 코드의 수정 없이 기능을 확장할 수 있다.**  
**인터페이스를 구현하는 클래스를 추가만 해주면 된다.**  
   
**결과적으로 OOP 핵심인 유지보수를 용이하기 위해, 약간 복잡하더라도 이러한 패턴을 적용하여 프로그램을 구성해 나가는 게 효율적이다.**

* * *

#### **전략 (Strategy) 패턴 적용 예시 1 - 여러 기능 전략을 가진 로봇**

-   **Robot이라는 추상클래스가 존재**
-   **걷는 로봇과 달리는 로봇으로 구성된 객체가 존재**

```java
public abstract class Robot {
    public abstract void display();
    public abstract void move();
}
```

```java
public class WalkingRobot extends Robot {
    @Override
    public void display() {
        System.out.println("걷는 로봇");
    }

    @Override
    public void move() {
        System.out.println("걸어서 배달합니다.");
    }
}
```

```java
public class RunningRobot extends Robot {
    @Override
    public void display() {
        System.out.println("달리는 로봇");
    }

    @Override
    public void move() {
        System.out.println("달려서 배달합니다.");
    }
}
```

```java
    @Test
    void test3() {
        Robot robot1 = new WalkingRobot();
        robot1.display();
        robot1.move();

        Robot robot2 = new RunningRobot();
        robot2.display();
        robot2.move();
    }
```

   
**보기에는 객체지향적으로 문제없는 코드이지만, 만약 로봇의 기능 추가가 된다면 코드의 유지보수면에서 문제가 발생한다. 만약 번역 기능도 추가한다고 가정하면, 새로운 메소드를 각 걷는 로봇에 추가하여야 한다.**   
 

-   **클래스가 4개로 늘어났다.**

```java
public class WalkingRobotKr extends Robot {
    @Override
    public void display() {
        System.out.println("걷는 로봇");
    }

    @Override
    public void move() {
        System.out.println("걸어서 배달합니다.");
    }

    @Override
    public void translate() {
        System.out.println("한국어로 번역합니다.");
    }
}
public class WalkingRobotJp extends Robot {
    @Override
    public void display() {
        System.out.println("걷는 로봇");
    }

    @Override
    public void move() {
        System.out.println("걸어서 배달합니다.");
    }

    @Override
    public void translate() {
        System.out.println("일본어로 번역합니다.");
    }
}

public class RunningRobotKr extends Robot {
    @Override
    public void display() {
        System.out.println("달리는 로봇");
    }

    @Override
    public void move() {
        System.out.println("달려서 배달합니다.");
    }

    @Override
    public void translate() {
        System.out.println("한국어로 번역합니다.");
    }
}
public class RunningRobotJp extends Robot {
    @Override
    public void display() {
        System.out.println("달리는 로봇");
    }

    @Override
    public void move() {
        System.out.println("달려서 배달합니다.");
    }

    @Override
    public void translate() {
        System.out.println("일본어로 번역합니다.");
    }
}
```

   
**위와 같은 '클래스 폭발' 문제가 일어난 이유는 객체를 사물 / 생물 정도로 밖에 인식하지 못해서이다.**  
   
**객체는 하나의 기능이나 행위, 동작으로도 표현할 수 있다.**  
**전략 패턴은 이러한 접근으로 복잡한 문제를 해결해 나가는 방식이다.**  
   
**행위를 구현체로 빼서 정의하고 관리해야 한다. 그리고 이 행위 객체들을 모아 인터페이스로 묶어 하나의 전략 묶음을  
구성하고, 이것을 컨텍스트에서 합성시켜 다형성을 통해 유기적으로 전략 행위들을 사용할 수 있도록 하는 것이다.**  
 

```java
public interface MoveStrategy {
    void move();
}
public class Walk implements MoveStrategy {
    @Override
    public void move() {
        System.out.println("걸어서 배달합니다.");
    }
}
public class Run implements MoveStrategy {
    @Override
    public void move() {
        System.out.println("달려서 배달합니다.");
    }
}
```

```java
public interface TranslateStrategy {
    void translate();
}
public class Korean implements TranslateStrategy {
    @Override
    public void translate() {
        System.out.println("한국어로 번역합니다.");
    }
}
public class Japanese implements TranslateStrategy {
    @Override
    public void translate() {
        System.out.println("일본어로 번역합니다.");
    }
}
```

-   **Context - 전략 등록/실행**

```java
public class RobotContext {
    MoveStrategy moveStrategy;
    TranslateStrategy translateStrategy;

    RobotContext (MoveStrategy moveStrategy, TranslateStrategy translateStrategy) {
        this.moveStrategy = moveStrategy;
        this.translateStrategy = translateStrategy;
    }

    void move() {
        moveStrategy.move();
    }

    void translate() {
        translateStrategy.translate();
    }

    void setMoveStrategy(MoveStrategy moveStrategy) {
        this.moveStrategy = moveStrategy;
    }

    void setTranslateStrategy(TranslateStrategy translateStrategy) {
        this.translateStrategy = translateStrategy;
    }
}
```

-   **Client - 전략 교체/설정, 실행한 결과를 얻음**

```java
    @Test
    void test4() {
        RobotStrategy robot = new RobotStrategy(new Walk(), new Korean());
        robot.move();
        robot.translate();

        robot.setMoveStrategy(new Run());
        robot.setTranslateStrategy(new Japanese());

        robot.move();
        robot.translate();
    }
```

* * *

#### **전략 (Strategy) 패턴 적용 예시 2 - 카드 결제 전략 시스템**

-   **1번과의 차이점은 전략 인터페이스를 클래스 필드로 합성하지 않고 Context의 메소드의 매개변수로 합성(Composition)한다.**

```java
public interface PaymentStrategy {
    void pay(int amount);
}
public class KAKAOCardStrategy implements PaymentStrategy {
    private String name;
    private String cardNumber;
    private String cvv;
    private String dateOfExpiry;

    public KAKAOCardStrategy(String name, String cardNumber, String cvv, String dateOfExpiry) {
        this.name = name;
        this.cardNumber = cardNumber;
        this.cvv = cvv;
        this.dateOfExpiry = dateOfExpiry;
    }

    @Override
    public void pay(int amount) {
        System.out.println(amount + "원을 카카오카드로 결제하였습니다.");
    }
}
public class LUNACardStrategy implements PaymentStrategy {
    private String emailId;
    private String password;

    public LUNACardStrategy(String emailId, String password) {
        this.emailId = emailId;
        this.password = password;
    }

    @Override
    public void pay(int amount) {
        System.out.println(amount + "원을 루나카드로 결제하였습니다.");
    }
}
```

```java
public class Item {
    public String name;
    public int price;

    public Item(String name, int price) {
        this.name = name;
        this.price = price;
    }
}
```

```java
public class ShoppingCart {
    List<Item> items;

    public ShoppingCart() {
        this.items = new ArrayList<>();
    }

    public void addItem(Item item) {
        this.items.add(item);
    }

    // 전략을 매개변수로 받는다
    public void pay(PaymentStrategy paymentStrategy) {
        int amount = 0;
        for(Item item : items) {
            amount += item.price;
        }
        paymentStrategy.pay(amount);
    }
}
```

```java
    @Test
    void test5() {
        // 쇼핑카트 전략 Context
        ShoppingCart cart = new ShoppingCart();

        // 쇼핑 물품
        Item a = new Item("맥북", 100000);
        Item b = new Item("핸드폰", 300000);
        cart.addItem(a);
        cart.addItem(b);

        // Luna 카드 결제 전략
        cart.pay(new LUNACardStrategy("jb@abc.com", "1234"));

        // Kakao 카드 결제 전략
        cart.pay(new KAKAOCardStrategy("jb", "123456", "123","01/12"));
    }
```

   
   
**메소드의 입력값으로 객체를 할당하는 방식이 좋은 점은,**  
**각 전략에 따라 초기화하는 생성자 매개변수 개수가 다를 수 있기 때문이다.**  
   
**코드에서 보다시피, Luna와 Kakao에서 필요로 하는 정보가 다르기 때문이다.**  
 

* * *

#### **전략 (Strategy) 패턴**

#### **장점**

-   **새로운 전략을 추가하더라도 기존 코드를 변경하지 않는다. (OCP)**
-   **상속대신 위임을 사용할 수 있다.**
-   **런타임에 전략을 변경할 수 있다.**
-   **기능을 호출하는 클라이언트는 내부 로직을 알 필요가 없다.**  
    **(캡슐화 - 의존성 분리 -> 테스트를 작성하기 편한 구조)**

#### **단점**

-   **알고리즘이 많아질수록 관리해야 할 객체의 수가 늘어난다.**
-   **애플리케이션 특성이 알고리즘이 많지 않고 자주 변경되지 않는다면, 새로운 클래스와 인터페이스를**  
    **만들어 프로그램을 복잡하게 만들 이유가 없다.**
-   **개발자는 적절한 전략을 선택하기 위해 전략 간의 차이점을 파악하고 있어야 한다. (복잡도 증가)**
