---
title: "[Effective Java] 아이템3 - private 생성자나 열거 타입으로 싱글턴임을 보증하라"
date: 2023-11-22 13:54:04 +0900
categories: ["Language", "Effective Java"]
tags: ["effective java item3", "effective java 아이템3", "이펙티브 자바 item3", "이펙티브 자바 아이템3", "이펙티브자바 item3", "이펙티브자바 아이템3"]
tistory_url: "https://aroma-bok.tistory.com/entry/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C3-private-%EC%83%9D%EC%84%B1%EC%9E%90%EB%82%98-%EC%97%B4%EA%B1%B0-%ED%83%80%EC%9E%85%EC%9C%BC%EB%A1%9C-%EC%8B%B1%EA%B8%80%ED%84%B4%EC%9E%84%EC%9D%84-%EB%B3%B4%EC%A6%9D%ED%95%98%EB%9D%BC"
---
#### **싱글턴 (Singleton)**

-   **인스턴스를 오직 하나만 생성할 수 있는 클래스**

#### **싱글턴 사용 이유**

-   **한 번의 객체 생성으로 재사용이 가능하기 때문에 메모리 낭비 방지**
-   **싱글톤으로 생성된 객체는 무조건 한번 생성으로 전역성을 띄기에 다른 객체와 공유가 용이**

#### **싱글턴을 만드는 방법**

1.  **public static 멤버가 final 필드인 방식**
2.  **정적 펙토리 메서드를 public static 멤버로 제공하는 방식**
3.  **원소가 하나인 열거 타입을 선언하는 방식**

* * *

#### **1. public static 멤버가 final 필드인 방식**

```java
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() {};

    public void leaveTheBuilding() { };
}
```

   
**private 생성자는 static final 필드인 INSTANCE를 초기화할 때, 딱 한 번만 호출된다.**  
**public이나 protected 생성자가 없으므로 Elvis 클래스가 초기화될 때 만들어진 인스턴스가 전체 시스템에서**  
**하나뿐임이** **보장된다.**

```java
public class Item3Test {
    @Test
    void singleTest() {
        Elvis elvis1 = Elvis.INSTANCE;
        Elvis elvis2 = Elvis.INSTANCE;

        assertSame(elvis1, elvis2);
    }
}
```

#### **public static 멤버가 final 필드인 방식 장점**

1.  **해당 클래스가 싱글턴임이 API에 명백히 드러난다.**  
    **public statc 필드가 final이니 절대로 다른 객체를 참조할 수 없다.**
2.  **간결하다.**

#### **예외**

-   **리플렉션 API인 AccessibleObject.setAccessible을 사용해 private 생성자를 호출할 수 있다.**

> **리플렉션 API : java.lang.reflect, class 객체가 주어지면, 해당 클래스의 인스턴스를 생성하거나 메서드를**  
> **호출하거나 필드에 접근할 수 있다.**

```java
    @Test
    void singleTest() throws IOException, NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException {
        Elvis elvis1 = Elvis.INSTANCE;
        Elvis elvis2 = Elvis.INSTANCE;

        assertSame(elvis1, elvis2);

        Constructor<Elvis> constructor = (Constructor<Elvis>) elvis2.getClass().getDeclaredConstructor();
        constructor.setAccessible(true);

        Elvis elvis3 = constructor.newInstance();
        assertNotSame(elvis2, elvis3);
    }
```

#### **해결방법**

-   **생성자를 수정하여 두 번째 객체가 생성되려 할 때 예외를 던지게 하면 된다.**

```java
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() {
        if(INSTANCE != null) {
            throw new RuntimeException("생성자가 이미 한번 실행되었습니다!");
        }
    };
    public void leaveTheBuilding() { };
}
```

```java
java.lang.RuntimeException: 생성자가 이미 한번 실행되었습니다!
```

* * *

#### **2. 정적 펙토리 메서드를 public static 멤버로 제공하는 방식**

```java
public class Elvis2 {
    public static final Elvis2 INSTANCE = new Elvis2();
    private Elvis2() { };
    
    public static Elvis2 getInstance() {
        return INSTANCE;
    }
    public void leaveTheBuilding() { };
}
```

   
**Elvis.getInstance()는 항상 같은 객체의 참조를 반환하므로 제2의 Elvis 인스턴스는 만들어지지 않는다.**  
**물론, 리플렉션을 통한 예외는 똑같이 적용된다.**

```java
public class Elvis2 {
    public static final Elvis2 INSTANCE = new Elvis2();
    private Elvis2() { };

    public static Elvis2 getInstance() {
        return INSTANCE;
    }
    public void leaveTheBuilding() { };
}
```

```java
    @Test
    void singleTest2() {
        Elvis2 elvis1 = Elvis2.getInstance();
        Elvis2 elvis2 = Elvis2.getInstance();

        assertSame(elvis1, elvis2);
    }
```

####  **정적 펙토리 메서드를 public static 멤버로 제공하는 방식 장점**

1.  **API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다.**  
    **getInstance() 호출하는 클라이언트의 수정 없이 내부에서 private static이 아닌 새 인스턴스를 생성해 주면 된다.**  
    **유일한 인스턴스를 반환하던 팩토리 메서드가 호출하는 스레드 별로 다른 인스턴스를 넘겨주게 할 수 있다.**
2.  **정적 팩토리를 제네릭 싱글턴 팩토리로 만들 수 있다.**  
    **제네릭 한 타입으로 동일한 싱글턴 인스턴스를 사용하고 싶을 때, 제네릭 싱글턴 팩토리를 만들어 사용할 수 있다.**  
    **인스턴스는 동일하지만, 각각의 타입으로 바꿔서 사용할 수 있다.**  
    **원하는 타입으로 형변환을 해줄 수 있다는 장점이 있다.**  
    
    ```java
        @Test
        void singleTest5() {
            Elvis4<String> elvis1 = Elvis4.getInstance();
            Elvis4<Integer> elvis2 = Elvis4.getInstance();
    
            // 제네릭 타입이 다르기 때문에 equals로 비교
            System.out.println(elvis1.equals(elvis2));
            // 출력 : true
        }
    ```
    
3.  **정적 팩토리의 메서드 참조를 공급자(Supplier)로 사용할 수 있다.**  
    **Supplier : get 메서드 만을 가지고 아무 type이나 리턴할 수 있는 인터페이스**

```java
        Supplier<Elvis2> elvis2Supplier = Elvis2::getInstance;
        Elvis2 elvis3 = elvis2Supplier.get();
        assertSame(elvis2, elvis3);
```

* * *

#### **위 두 방식의 공통 문제점**

-   **각 클래스를 직렬화한 후, 역직렬화 할 때 새로운 인스턴스를 만들어서 반환한다.**
-   **역직렬화는 기본 생성자를 호출하지 않고 값을 복사해서 새로운 인스턴스를 반환한다.  
    이런 경우에, 싱글턴을 보장해주는 방법이 readResolve() 메서드이다.**
-   **readResolve() 메서드는 역직렬화 시 호출되는 메서드이다.**

**readRsolve()에서 싱글턴 인스턴스를 반환하고, 모든 필드에 transient(직렬화 제외) 키워드를 넣는다.**  
**싱글턴 클래스를 직렬화하려면 단순히 Serializable을 구현하고 선언하는 것만으로는 부족하다.**  
**모든 인스턴스 필드를 일시적(transient)라고 선언하고 readResolve() 메서드를 제공해야 한다.**  
   
**이렇게 하지 않으면 직렬화된 인스턴스를 역직렬화할 때마다 새로운 인스턴스가 만들어진다.**  
**가짜 Elvis 탄생을 예방하고 싶다면 Elvis 클래스에 다음의 readResolve 메서드를 추가하자.**

```java
    @Test
    void singleTest3() {
        try (ObjectOutput out = new ObjectOutputStream(new FileOutputStream("elvis2.obj"))) {
            out.writeObject(Elvis2.INSTANCE);
        } catch (IOException e) {
            e.printStackTrace();
        }

        try (ObjectInput in = new ObjectInputStream(new FileInputStream("elvis2.obj"))) {
            Elvis2 elvis3 = (Elvis2) in.readObject();
            System.out.println(elvis3 == Elvis2.INSTANCE); // 역직렬화시 새로운 인스턴스가 생긴다.
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
```

   
**결과는 당연히 false이다.**

```java
false
```

   
**역직렬화 시 호출되는 메서드를 아래처럼 수정해서, 역직렬화가 되어도 기존 객체를 리턴하도록 수정한다.**

```java
public class Elvis2 implements Serializable {
    public static final Elvis2 INSTANCE = new Elvis2();
    private Elvis2() { };

    public static Elvis2 getInstance() {
        return INSTANCE;
    }
    public void leaveTheBuilding() { };
    
    private Object readResolve() {
        return INSTANCE;
    }
}
```

   
**결과는 true! 진짜 Elvis를 반환하고, 가짜 Elvis는 가비지 컬렉터에 맡긴다.**

```java
true
```

* * *

#### **3. 원소가 하나인 열거 타입을 선언하는 방식**

-   **대부분의 상황에서 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법이다.**

```java
public enum Elvis3 {
    INSTANCE;

    public String getName() {
        return "Elvis";
    }
}
```

```java
    @Test
    void singleTest4() {
        Elvis3 elvis3 = Elvis3.INSTANCE;
        System.out.println(elvis3.getName());
        // 출력 Elvis
    }
```

#### **원소가 하나인 열거 타입을 선언하는 방식 장점**

1.  **리플렉션과 직렬화, 역직렬화에 굉장히 안전한 방법이다.**
2.  **Enum은 리플렉션을 내부코드로 막아 놓았기 때문에 생성자를 불러오려고 하면 에러가 발생한다.**
3.  **Enum은 생성자를 통해 인스턴스를 만들 수 없다.**
4.  **Enum은 인터페이스를 구현할 수 있기에 테스트 코드 작성 시 문제까지도 해결할 수 있다.**
