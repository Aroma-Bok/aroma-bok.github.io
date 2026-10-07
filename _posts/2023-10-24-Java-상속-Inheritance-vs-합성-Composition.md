---
title: "[Java] 상속 (Inheritance) vs 합성 (Composition)"
date: 2023-10-24 19:28:13 +0900
categories: ["Language", "Java"]
tags: ["inheritance composition", "java inheritance composition", "java 상속 합성", "상속 합성 차이", "자바 상속 합성", "합성 vs 상속"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-%EC%83%81%EC%86%8D-Inheritance-vs-%ED%95%A9%EC%84%B1-Composition"
---
#### **상속(Inheritance)**

-   **클래스 상속을 통해 자식 클래스는 부모 클래스의 자원을 물려받게 되며, 부모 클래스와 다른 부분만 추가하거나 재정의함으로써 기존 코드를 쉽게 확장할 수 있다.**
-   **부모 클래스의 내부 구현에 대해 상세하게 알아야 하기 때문에 자식 클래스와 부모 클래스 사이의 결합도가 높다.**
-   **상속 관계는 컴파일 단계에서 결정되고 고정되기 때문에 코드를 실행하는 도중에 변경할 수 없다.**
-   **Is-a 관계**

```java
public class Person {
    // 상속 개념

    public void work() {
        System.out.println("걷다");
    }

    public void talk() {
        System.out.println("말하다");
    }
}
```

```java
public class Developer extends Person{
    // 개발자는 사람 클래스를 상속 받았으므로 사람 클래스에 정의된 메소드들을 사용할 수 있다.
}
```

-   **Pserson 클래스를 상속받은 Developer 클래스는 Person 클래스에 정의된 메소드들을 아래와 같이 사용 가능**

```java
    Person developer = new Developer();
    developer.talk();
    developer.work();
```

**부모 클래스에 정의된 메소드를 물려받아 재사용하는 것을 상속(Inheritance)이라고 부른다.**

* * *

#### **합성(Composition)**

-   **기존 클래스를 상속을 통한 확장하는 대신에, 필드로 클래스의 인스턴스를 참조하게 만드는 설계이다.**
-   **합성은 객체 간의 관계가 수직관계가 아닌 수평관계가 된다.**
-   **Has-a 관계**

-   **예를 들어, 자동차와 엔진종류 간의 관계 같이 아주 연관이 없지는 않지만 상속 관계로 맺기에는 애매한 것들을 다루는 것으로 볼 수도 있다.**

```java
public class Car {
    Engine engine; // 필드로 Engine 클래스 변수를 갖는다 (Has)

    public Car(Engine engine) {
        this.engine = engine;
    }

    public void drive() {
        System.out.printf("%s 엔진으로 드라이브한다.", engine.EngineType);
    }

    public void breaks() {
        System.out.printf("%s 엔진으로 브레이크한다.", engine.EngineType);
    }
}
```

```java
    @Test
    void test1() {
        Car digelCar = new Car(new Engine("디젤"));
        digelCar.drive();

        Car gasolineCar = new Car(new Engine("가솔린"));
        digelCar.drive();
    }
```

**위의 초기화 코드에서 볼 수 있듯이, 마치 new 생성자에 new 생성자를 받는 형식 new Car(new Engine("디젤")) 으로  
쓰인다.** 

**즉, Car 클래스가 Engine 클래스의 기능이 필요하다고 무조건 상속하지 말고, 따로 클래스 인스턴스 변수에 저장하여  
가져다 쓴다는 원리이다.**

**이러한 방식을 포워딩(Forwarding)이라고 하며 필드의 인스턴스를 참조해 사용하는 메소드를 포워딩 메소드(Forwarding Method)라고 부른다.**

**상속으로 코드를 재사용하는 것과 합성으로 재사용하는 것은 근본적으로 다르다.**

**이유는 합성을 이용하면 객체의 내부는 공개되지 않고 인터페이스를 통해 코드를 재사용하기 때문에 구현에 대한 의존성을 인터페이스에 대한 의존성으로 변경하여 결합도를 낮출 수 있기 때문이다.**

* * *

#### **상속보다 합성을 사용해야 하는 이유?**

-   **상속은 중복을 제거하기에 좋은 객체지향 기술처럼 보이고, 그에 따라 상속을 남발하는 경우를 자주 볼 수 있다.  
    하지만, 상속을 이용해야 하는 경우는 상당히 선택적이다.**
-   **상속이 갖는 단점은 상당히 치명적이기 때문에 상속보다는 합성을 이용하는 것을 권장한다.**
-   **현업에서도 가능하면 extends를 지양한다고 한다.**

#### **상속의 대표적 단점**

1.  **캡슐화가 깨지고 결합도가 높아짐**
2.  **유연성 및 확장성이 떨어짐**
3.  **다중상속에 의한 문제 발생 우려**
4.  **클래스 폭발 문제 발생 우려**
5.  **메서드 오버라이딩의 오동작**

#### **1\. 캡슐화가 깨지고 결합도가 높아짐**

**→ 결합도는 하나의 모듈이 다른 모듈에 대해 얼마나 많은 지식을 갖고 있는지를 나타내는 정도이다. 객체지향 프로그래밍에서는 결합도는 낮을수록, 응집도는 높을수록 좋다. 그리고 객체지향의 장점 중 하나는 추상화에 의존함으로써 다른 객체에 대한 결합도는 최소화하고 응집도를 최대화하여 변경 가능성을 최소화하는 것이다.**

**하지만, 상속은 부모 클래스와 자식 클래스의 관계가 컴파일 시점에 결정되어 구현에 의존하기 때문에 캡슐화가 깨지고 결합도가 높아진다.** 

**컴파일 시점에 결정되는 관계는 유연성을 상당히 떨어뜨리고, 실행 시점에 객체의 종류를 변경하는 것이 불가능하여 다형성 등과 같은 객체지향 기술을 사용할 수없다.**

**또한 부모클래스의 코드를 직접 호출가능하므로 부모 클래스의 내부 구조를 잘 알고 있어야 한다.**

-   **Develop 추상 클래스와 이를 구현한 ProjectA  클래스가 있다**

```java
@Data
@RequiredArgsConstructor
public abstract class Develop {
    private final int price;
    public int calculatePrice() {
        return price;
    }
}
```

```java
public class ProjectA extends Develop{

    public ProjectA(final int price) {
        super(price);
    }
}
```

-   **그리고 ProjectA와 ProjectB로 구성된 세트 항목을 추가하며, ProjectA를 같이 구매하면 할인이 되어 계산되는 discountAmount()를 추가하고 ProjectA를 상속받아 세트로 만들어진 ProjectAWithB 클래스와 부모 Develop 클래스의 calculatePrice() 메소드를 오버라이딩하여 상속으로 구현하고 있다**

```java
public class ProjectA extends Develop{

    public ProjectA(final int price) {
        super(price);
    }

    protected int discountAmount() {
        return 5000;
    }
}
```

```java
public class ProjectAWithB extends ProjectA{
    public ProjectAWithB(int price) {
        super(price);
    }

    @Override
    public int calculatePrice() {
        // 원래 금액 - ProjectA와 B를 동시에 구매하면 할인되는 금액
        return super.calculatePrice() - super.discountAmount();
    }
}
```

**ProjectAWithB는 할인 금액을 위해 구현 클래스인 ProjectA 클래스에 의존하고 있다.**

**ProjectAWithB에서 할인금액을 계산하기 위해서는 부모 클래스인 ProjectA 클래스에서 discountAmount() 제공해야 함을 볼 수 있다.**

**이렇게 애플리케이션이 실행되는 시점이 아닌, 컴파일 시점에 ProjectAWithB가 ProjectA라는 구현 클래스에 의존하는 것을 컴파일 타임 의존성이라 부르고, 이는 다형성을 사용할 수 없어 객체지향적이지 못하다.**

**만약, 해당 메소드의 이름이 변경되면 자식 클래스의 메소드도 수정해야 하는 문제도 발생한다.**

**즉, 상속을 이용하기 위해서는 부모 클래스의 내부 구조를 알고 있어야 한다.** 

**부모클래스를 기반으로 자식 클래스의 코드를 구현해야 하기 때문이다. 자식 클래스에서 super 키워드를 이용해 부모 클래스의 메소드를 호출하는 상황이라면 부모 클래스의 구현은 자식 클래스에게 노출되어 캡슐화가 약해지고,  
자식 클래스와 부모 클래스는 강하게 결합되어 부모 클래스를 변경할 때 자식 클래스도 함께 변경해야 할 가능성이 높아진다.**

* * *

#### **2\. 유연성 및 확장성이 떨어짐**

**→ 부모 클래스와 자식 클래스가 강하게 결합되므로 유연성과 확장성이 상당히 떨어진다.**

**예로, Develop 추상 클래스에 개발한 프로젝트 수를 반환하는 새로운 메소드를 추가해야 하는 상황이라면, 해당 구현 클래스에도 추가적으로 수정이 필요하다.**

```java
public class ProjectA extends Develop{

    public ProjectA(final int price,final int count) {
        super(price, count); // 수정
    }

    protected int discountAmount() {
        return 5000;
    }
}
```

```java
public class ProjectAWithB extends ProjectA{
    public ProjectAWithB(int price, int count) {
        super(price, count); // 수정
    }

    @Override
    public int calculatePrice() {
        // 원래 금액 - ProjectA와 B를 동시에 구매하면 할인되는 금액
        return super.calculatePrice() - super.discountAmount();
    }
}
```

-   **그리고 객체를 생성하는 부분들 역시 모두 수정해야 한다.**

```java
//        ProjectAWithB project = new ProjectAWithB(10000);
        ProjectAWithB project = new ProjectAWithB(10000, 2);
        System.out.println(project);
```

* * *

#### **3\. 클래스 폭발 문제 발생 우려**

**→ 상속을 남용하면 필요 이상으로 많은 클래스를 추가해야 하는 클래스 폭발 문제가 발생할 수 있다.**

**만약 새로운 요구사항이 생겨 ProjectA와 ProjectB 그리고 ProjectC로 구성된 메뉴를 추가해야 하는 상황이라고 하자.**

**그러면, 이를 해결하기 위해서 새로운 클래스를 또 추가해야 한다.**

**계속해서 새로운 요구사항이 생겨난다면 이러한 클래스를 계속적으로 추가해야 할 것이다.**

-   **새로운 클래스 생성**

```java
public class ProjectAWithBAndC extends ProjectAWithB{
    public ProjectAWithBAndC(int price, int count) {
        super(price, count);
    }
}
```

**클래스 폭발 문제는 자식 클래스가 부모 클래스의 구현과 강하게 결합되도록 강요하는 상속의 근본적인 한계 때문에 발생한다. 컴파일 타임에 결정된 자식 클래스와 부모 클래스 사이의 관계는 변경될 수 없기에 자식 클래스와 부모 클래스의 다양한 조합이 필요한 상황에서 유일한 해결 방법은 새로운 클래스를 추가하는 것뿐이다.**

* * *

#### **4\. 다중 상속에 의한 문제가 발생할  우려**

**→ 자바에서는 다중 상속을 허용하지 않는다. 상속이 필요한 클래스가 다른 클래스를 이미 상속 중이라면 문제가 발생할 수 있다. 이 문제를 피하기 위해서라도 상속의 사용을 지양해야 한다.**

* * *

#### **5\. 메서드 오버라이딩의 오동작**

**→ 자식 클래스가 부모 클래스의 메소드를 오버라이딩 할 때, 자식 클래스가 부모 클래스의 메소드 호출 방법에 영향을 받는 문제이다.**

**부모의 public 메소드는 외부에서 사용하도록 노출한 메소드이다. 그런데 상속을 하게 된다면, 자식 클래스에서도  
부모 클래스의 public 메소드를 이용할 때, 의도하지 않은 동작을 수반할 수 있게 된다.**

**이는 캡슐화를 위반한다고 할 수 있다.**

> **캡슐화**  
> **→ 단순히 private 변수로 Getter/Setter를 얘기하는 것이 아니다.**  
> **캡슐화(정보 은닉)는 객체가 내부적으로 기능을 어떻게 구현하는지를 감추는 것을 말한다.**  
> **그래서 우리는 클래스 자료형을 이용할 때, 내부 동작을 알 필요 없이 단순히 메소드만 갖다 쓰면 된다.**  
> **단, 내부 동작을 알 필요가 없다는 말은 신뢰성이 보장되어야 한다는 말이기도 하다.**  
> **캡슐화가 깨진 건 이러한 신뢰성이 깨진 것이라 보면 된다.**

-   **다음 코드는 자바의 HashSet을 상속하고 부모의 메소드를 오버라이딩 하여서 커스텀 Set 클래스를 만들어 구축한 코드다.**

```java
public class CustomSet<E>  extends HashSet<E> {
    private int addCount = 0; // 몇번 추가되었는지 카운트하는 변수

    @Override
    public boolean add(E e) {
        // add되면 카운트 증가시키고, 부모 클래스(HashSet)의 add() 메소드 실행.
        addCount++;
        return super.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c){
        // 리스트 자체로 들어와 통째로 add 하면, 컬렉션의 사이즈를 구해 카운트에 더한다.
        // 그리고 부모 클래스(HashSet)의 addAll() 메소드 실행.
        addCount += c.size();
        return super.addAll(c);
    }

    public int getAddCount() {
        return addCount;
    }
}
```

```java
    @Test
    void test2() {
        CustomSet<String> mySet = new CustomSet<>();

        mySet.addAll(Arrays.asList("가","나","다","라","마"));
        mySet.add("바");

        System.out.println(mySet.getAddCount());
    }
```

**원했던 결과는 6이 나와야 하지만, 실행해서 확인하면 11이라는 결과가 나온다.**

**문제는 부모의 addAll() 메소드를 함부로 오버라이딩해서 부모의 메소드를 사용했기 때문이다.**

**부모의 allAll() 메소드 내부를 보면 루프를 돌면서 add() 메소드를 호출하고 있다.**

```java
    public boolean addAll(Collection<? extends E> c) {
        boolean modified = false;
        for (E e : c)
            if (add(e))
                modified = true;
        return modified;
    }
```

**결국 부모 클래스의 내부 로직을 뒤져서 자세히 살펴봐야 할 필요성이 생겨나고, 부모 클래스에서 수정이라도 한다면**

**하위 클래스는 오동작을 일으킬 수 있는 위험성이 존재한다.**

* * *

#### **합성(Composition) 사용하기**

**→ 상속 관계는 클래스 사이의 정적인 관계지만, 합성 관계는 객체 사이의 동적인 관계이다.**

**코드 작성 시점에 결정한 상속 관계는 변경이 불가능(컴파일 타임) 하지만, 합성 관계는 실행 시점에 동적으로 변경할 수 있기 때문이다 (런타임).**

**합성은 내부에 포함되는 객체의 구현이 아닌 퍼블릭 인터페이스에 의존한다.**

**그래서 객체 내부의 구현이 변경되더라도 영향을 최소화할 수 있기 때문에 변경/수정에 어느 정도 안정적이다.**

**코드의 재사용적인 면에서도 상속보다는 합성이 더 좋은 방법이다.**

-   **HashSet을 상속하는 CustomSet 예제를 합성으로 변환해 보자**

```java
public class CustomSetForComposition<E>  {
    private int addCount = 0; // 몇번 추가되었는지 카운트하는 변수
    private Set<E> set = new HashSet<>(); //합성

    public boolean add(E e) {
        // add되면 카운트 증가시키고, 부모 클래스(HashSet)의 add() 메소드 실행.
        addCount++;
        return set.add(e); // 합성된 객체의 메서드를 실행
    }

    public boolean addAll(Collection<? extends E> c){
        addCount += c.size();
        return set.addAll(c); // 합성된 객체의 메서드를 실행
    }

    public int getAddCount() {
        return addCount;
    }
}
```

```java
    @Test
    void test3() {
        CustomSetForComposition<String> mySet = new CustomSetForComposition<>();

        mySet.addAll(Arrays.asList("가","나","다","라","마"));
        mySet.add("바");

        System.out.println(mySet.getAddCount()); // 결과 : 6
    }
```

* * *

**합성은 단순히 메소드 호출을 통해 값을 사용하면 되기 때문에 구현이 어렵지도 않다.**

**그러나 합성에도 단점이 존재하는데, 아무래도 상속과는 달리 클래스 간의 관계를 파악하는 데 있어 시간이 걸린다는 점이다. 즉, 코드가 복잡해질 수 있다는 점을 떠안고 있다.**

**그래도 현업에서도 상속을 지양하고 합성을 지향한다고 한다. 상속은 반드시 어떠한 특정한 관계일 때만 사용하고  
그 외의 경우에는 합성을 통해 클래스를 구성해 보자.**

* * *

**정리하는데 참고한 곳**

**1\. [https://inpa.tistory.com/entry/OOP-%F0%9F%92%A0-%EA%B0%9D%EC%B2%B4-%EC%A7%80%ED%96%A5%EC%9D%98-%EC%83%81%EC%86%8D-%EB%AC%B8%EC%A0%9C%EC%A0%90%EA%B3%BC-%ED%95%A9%EC%84%B1Composition-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0](https://inpa.tistory.com/entry/OOP-%F0%9F%92%A0-%EA%B0%9D%EC%B2%B4-%EC%A7%80%ED%96%A5%EC%9D%98-%EC%83%81%EC%86%8D-%EB%AC%B8%EC%A0%9C%EC%A0%90%EA%B3%BC-%ED%95%A9%EC%84%B1Composition-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0)**

[💠 상속을 자제하고 합성(Composition)을 이용하자](https://inpa.tistory.com/entry/OOP-%F0%9F%92%A0-%EA%B0%9D%EC%B2%B4-%EC%A7%80%ED%96%A5%EC%9D%98-%EC%83%81%EC%86%8D-%EB%AC%B8%EC%A0%9C%EC%A0%90%EA%B3%BC-%ED%95%A9%EC%84%B1Composition-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0)

2\. [https://mangkyu.tistory.com/199](https://mangkyu.tistory.com/199)
