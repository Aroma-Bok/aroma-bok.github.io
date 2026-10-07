---
title: "[Design Pattern] 구조 패턴 - Composite"
date: 2023-10-26 09:45:47 +0900
categories: ["Language", "Design Pattern"]
tags: ["composite patteren", "design pattern composite", "java composite", "디자인패턴 composite", "디자인패턴 composite 백기선", "디자인패턴 컴포짓 백기선", "컴포짓 패턴"]
tistory_url: "https://aroma-bok.tistory.com/entry/Design-Pattern-%EA%B5%AC%EC%A1%B0-%ED%8C%A8%ED%84%B4-Composite"
---
### **Composite 패턴**

-   **전체 계층 구조와 전체를 구성하는 부분 계층 구조를 클라이언트 입장에서 동일하게 바라볼 수 있도록 구성하는 패턴**
-   **클라이언트에서 개별 객체와 다른 객체들로 구성된 복합 객체 (Composite)를 똑같은 방법으로 다룰 수 있다.**
-   **클라이언트 입장에서는 '전체' 나 '부분'이나 모두 동일한 컴포넌트로 인식할 수 있는 계층 구조를 만든다.**
-   **트리 구조에 종속적**

* * *

#### **Composite 패턴 구현 방법**

-   **클라이언트는 Component 인터페이스에서 정의한 메서드만을 사용하도록 한다**
-   **개발자가 생성할 객체들은 Leaf 혹은 Composite 타입으로 구현**
-   **Leaf는 가장 Primitive 한 타입이고 Composite은 Primitive 한 타입들을 그룹으로 가지고 있는 객체**
-   **따라서 Composite은 여러 개의 Component를 배열 혹은 리스트로 가지고 있다.**
-   **Composite의 배열이나 리스트의 타입도 Leaf가 아닌 Component이고  Component에 정의된 기능을 제공하기  
    때문에 일관된 연산을 수행할 수 있다.**

![](/assets/img/posts/238/1.png)

* * *

#### **Composite 패턴 적용 전**

```java
@Data
public class Item {
    private String name;
    private int price;

    public Item(String name, int price) {
        this.name = name;
        this.price= price;
    }
}
```

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Bag {
    private List<Item> items = new ArrayList<>();

    public void add(Item item) {
        items.add(item);
    }
}
```

```java
 @Test
    void test1() {
        Item A = new Item("A", 550);
        Item B = new Item("B", 300);

        Bag bag = new Bag();
        bag.add(A);
        bag.add(B);

        System.out.println(bag.getItems().stream().mapToInt(Item::getPrice).sum());
    }
```

***클라이언트 입장에서 모든 정보를 가져가기 때문에 객체지향적으로 봐도 옳지 않다.***  
**가방 안에 가방이 들어있거나 가방이 2개가 있다면 또 다른 메서드를 클라이언트에 구현을 해야 합니다.**  
***결국 나중에는 코드 변경도 클라이언트 부분에서 할 필요가 생긴다는 겁니다.***

**이제는 클라이언트가 바라보는 파라미터를 하나의 최상위 Component로 추상화를 하면 클라이언트는 하위 로직은  
전혀 몰라도 되고, Component의 가격을 물어보기만 하면 된다.**

* * *

#### **Composite 패턴 적용 후**

-   **클라이언트가 바라 볼 최상위 Component 생성**

```java
public interface Component {
    int getPrice();
}
```

-   **Leaf 클래스인 Bag과 Item은 최상위 컴포넌트를 구현**
-   **Bag에서 Component를 담는 List를 가진 이유는 트리구조를 만들기 위해서이다.**

```java
@Getter
public class BagForComposite implements Component{

    private List<Component> components = new LinkedList<>();

    public void add(Component component) {
        components.add(component);
    }

    @Override
    public int getPrice() {
        return components.stream().mapToInt(Component::getPrice).sum();
    }
}
```

```java
public class ItemForComposite implements Component{
    private String name;
    private int price;

    public ItemForComposite (String name, int price) {
        this.name = name;
        this.price = price;
    }
    @Override
    public int getPrice() {
        return this.price;
    }
}
```

-   **클라이언트는 최상위 Component만 바라보면 된다.**
-   **아까처럼 모든 정보를 클라이언트가 알 필요가 없다.**

```java
    @Test
    void test2() {
        ItemForComposite A = new ItemForComposite("A", 550);
        ItemForComposite B = new ItemForComposite("B", 300);

        BagForComposite bag = new BagForComposite();
        bag.add(A);
        bag.add(B);

        // 클라이언트는 가장 상위를 바라본다. 즉, 구체적인 방법을 알필요가 없다.
        System.out.println(bag.getPrice());
        System.out.println(A.getPrice());
    }
```

* * *

#### **Composite 활용 상황**

-   **객체들 간에 계급 및 계층구조가 있고 이를 표현해야 할 때 유용**
-   **클라이언트가 단일 객체와 집합 객체를 구분하지 않고 동일한 형태로 사용하고자 할 때 유용**

* * *

#### **Composite 패턴**

**장점**

-   **복잡한 트리 구조를 편리하게 사용 가능**
-   **최상위 클래스를 바라보기에 다형성과 재귀를 활용할 수 있다.**
-   **클라이언트가 코드를 변경하지 않고 새로운 엘리먼트 타입을 추가할 수 있다.**  
    **객체들이 모두 같은 타입(Component)으로 취급되기 때문에 새로운 클래스 추가가 용이**
-   **단일 객체 및 집합 객체를 구분하지 않고 코드 작성이 가능하여 사용자 코드가 단순해진다.**

**단점**

-   **트리를 만들어야 하기 때문에 ( = 공통된 인터페이스를 정의) 지나치게 일반화해야 하는 경우도 생길 수 있다.**
-   **설계가 지나치게 많은 범용성을 가진다.**
