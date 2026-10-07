---
title: "[Effecttive Java] 아이템1 - 생성자 대신 정적 팩터리 메서드를 고려하라"
date: 2023-11-21 15:04:42 +0900
categories: ["Language", "Effective Java"]
tags: ["effective java", "effective java item1", "effective java 아이템1", "이펙티브 자바 item1", "이펙티브 자바 생성자 대신 정적 팩터리 메서드를 고려하라"]
tistory_url: "https://aroma-bok.tistory.com/entry/Effecttive-Java-%EC%95%84%EC%9D%B4%ED%85%9C1-%EC%83%9D%EC%84%B1%EC%9E%90-%EB%8C%80%EC%8B%A0-%EC%A0%95%EC%A0%81-%ED%8C%A9%ED%84%B0%EB%A6%AC-%EB%A9%94%EC%84%9C%EB%93%9C%EB%A5%BC-%EA%B3%A0%EB%A0%A4%ED%95%98%EB%9D%BC"
---
####  **클래스가 객체를 생성하는 방법에는 2가지가 있다.**

1.  **Public 생성자**  
    **→ public 생성자를 통한 객체 생성은 일반적으로 알고 있는 'new' 키워드를 사용하는 방법**
2.  **정적 팩토리 메서드**  
    **→  'new'를 직접적으로 사용하지 않고 클래스 내에 선언된 메서드 내부에서 'new'를 사용해 객체를 리턴하는 방법**

* * *

#### **정적 팩토리 메서드 예시**

**String 클래스에서 구현되어 있는 정적 팩토리 메서드이다. 넘겨받은 파라미터로 new를 통해 String 객체를 생성한다.**

```java
    /**
     * Returns the string representation of the {@code char} array
     * argument. The contents of the character array are copied; subsequent
     * modification of the character array does not affect the returned
     * string.
     *
     * @param   data     the character array.
     * @return  a {@code String} that contains the characters of the
     *          character array.
     */
    public static String valueOf(char data[]) {
        return new String(data);
    }
```

* * *

#### **public 생성자 방법 vs 정적 팩토리 메서드 방법**

```java
public class Item1 {
    public static void main(String[] args) {
        String str1 = String.valueOf("정적 팩토리를 통한 생성");
        String str2 = new String("new를 통한 생성");

        System.out.println(str1);
        System.out.println(str2);
    }
}
```

```java
정적 팩토리를 통한 생성
new를 통한 생성
```

**두 방법 모두 String 객체를 반환했다.**

**이렇게 보면 두 방법에 대한 차이가 없어 보이는데, 왜 정적 팩토리 메서드 방식이 선호될까?**

* * *

#### **정적 팩토리 메서드 장점**

#### **장점 1 : 이름을 가질 수 있다**

**다음과 같이 Order 클래스가 존재할 때, Order 생성자 생성 시 메서드 시그니처가 같으면 에러가 발생하기 때문에**

**매개변수의 순서를 다르게 해서 생성할 순 있다.**

**하지만, 생성자는 클래스 명과 동일하게 이름을 사용해야 하는 규칙 때문에 Order라는 생성자의 이름만 봐서는**

**prime 객체를 생성하는 것인지, urgent 객체를 생성하는 것인지 알 수 없다. 즉, 무엇을 생성하는 것인 알 수 없다.**

```java
public class Order {
    private boolean prime;
    private boolean urgent;
    private Product product;

    public Order(Product product, boolean urgent) {
        this.product = product;
        this.urgent = urgent;
    }

    public Order(boolean urgent, Product product) {
        this.urgent = urgent;
        this.product = product;
    }
}
```

**생성자에게 넘기는 매개변수와 생성자 자체만으로는 반환될 객체의 특성을 설명하지 못하는 문제점을 해결할 수 있는 방법이 정적 팩토리 메서드 방법이다.**

**정적 팩토리 메서드를 사용하면 이름을 작성할 수 있게 되니까 리턴될 객체를 잘 표현할 수 있게 된다.**

```java
public class Order {
    private boolean prime;
    private boolean urgent;
    private Product product;

    public static Order primeOrder(Product product) {
        Order order = new Order();
        order.product = product;
        order.prime = true;
        return order;
    }

    public static Order urgentOrder(Product product) {
        Order order = new Order();
        order.product = product;
        order.urgent = true;
        return order;
    }
}
```

* * *

#### **장점 2 :  호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.**

**Java의 public 생성자는 매번 호출될 때마다 새로운 인스턴스를 만든다.**

**public 생성자가 있다면 인스턴스 통제가 불가능하다는 말이다.**

**다음 Settings 클래스를 보면 생성자를 호출할 때마다 주소값이 다르게 출력되었고,**

**매번 새로운 인스턴스가 생성되는 걸 확인할 수 있다.**

```java
public class Settings {
    private boolean useAutoSteering;
    private boolean useABS;
    private Diffculty diffculty;

    public static void main(String[] args) {
        System.out.println(new Settings());
        System.out.println(new Settings());
        System.out.println(new Settings());
    }
}
```

```java
EffectiveJava.Item1.Settings@13969fbe
EffectiveJava.Item1.Settings@6aaa5eb0
EffectiveJava.Item1.Settings@3498ed
```

**인스턴스를 단 하나만 만들어야 하는 상황이라면, 정적팩토리 메서드를 통해 만들 수 있다.**

**여러 개의 인스턴스가 생성되는 걸 막기 위해 생성자를 private으로 막아놓고, 지정한 인스턴스만 생성되게 하기 위해** 

**static final로 SETTINGS를 미리 생성해 놓고 정적 팩토리 메서드를 사용해 SETTINGS 인스턴스를 리턴하게 한다.**

**이렇게 해주면 다른 클래스에서 Settings 클래스의 인스턴스를 생성하고 싶을 때 getInstance라는 정적 팩토리 메서드를 통하는 방법밖에 없다. 그리고 getInstance를 여러 개 호출해도 같은 주소값이 출력되는 걸 확인할 수 있는데**

**호출될 때마다 인스턴스가 새로 생성되지 않게 된다.**

**즉, 정적 팩토리 메서드 내에서 인스턴스 통제가 가능해졌다.**

```java
public class Settings {
    private boolean useAutoSteering;
    private boolean useABS;
    private Diffculty diffculty;

    // 생성자를 private으로 설정 (외부 호출 불가)
    private Settings() { };

    public static final Settings SETTINGS = new Settings();

    public static Settings getInstance() {
        return SETTINGS;
    }

    public static void main(String[] args) {
        Settings settings1 = Settings.getInstance();
        Settings settings2 = Settings.getInstance();

        System.out.println(settings1);
        System.out.println(settings2);
    }
}
```

```java
EffectiveJava.Item1.Settings@22f71333
EffectiveJava.Item1.Settings@22f71333
```

* * *

#### **장점 3 : 반환 타입의 하위 타입 객체를 반환할 수 있다.**

#### **장점 4 : 입력 매개 변수에 따라 매번 다른 객체를 반환할 수 있다.**

**3번과 4번에 부합하는 코드는 다음과 같다.**

**다음 코드는 Grade라는 인터페이스를 A, B, C 클래스가 구현하고 있고, 정적 팩토리 메서드인 of는 Grade 타입의 객체를**

**리턴하고 있다. 즉, 반환 타입의 하위 타입 객체라면 선택적으로 다양한 객체를 반환하는 것이 가능하게 된다.**

**이렇게 하면, 구현 로직을 숨길 수 있어서 API가 경량화된다.**

**또한, of 메서드 내부의 if문 조건에 따라 다른 객체를 리턴하는 것도 가능해진다. (유연성이 좋아진다.)**

```java
public interface Grade {
    String toText();

    public static Grade of(int score) { // 반환타입 == Grade
        if(score >= 90) { // Grade를 구현하는 A,B,C를 선택적으로 다양한 객체로 반환할 수 있다.
            return new A();
        }else if(score >= 80) {
            return new B();
        }else {
            return new C();
        }
    }
}
```

```java
public class A implements Grade {
    @Override
    public String toText() {
        return "A";
    }
}
public class B implements Grade {
    @Override
    public String toText() {
        return "B";
    }
}
public class C implements Grade {
    @Override
    public String toText() {
        return "C";
    }
}
```

* * *

#### **장점 5 : 정적 팩토리 메서드를 작성하는 시점에서 반환할 객체의 클래스가 존재하지 않아도 된다.**

**클래스가 존재해야 생성자가 존재할 수 있다. 하지만 정적 팩토리 메서드는 메서드와 반환할 타입만 정해두고 실제 반환될**

**클래스는 나중에 구현하는 게 가능하다. 프래그램의 규모가 거대해지면 여러 개발자들과 협업을 하게 되는데**

**원활한 협업을 위해 인터페이스까지 먼저 협의하여 만들고, 실제 구현체는 추후 구현한다는 식으로 업무가 진행된다.**

**이때 정적 팩토리 메서드를 사용할 수 있다.**

```java
public abstract class Company {
    public static Company getInstance(String path) {
        Company company = null;
        try {
            Class<?> childCompany = Class.forName(path);
            company = (Company) childCompany.newInstance();

        } catch (ClassNotFoundException e) {
            System.out.println("클래스가 없습니다.");
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InstantiationException e) {
            e.printStackTrace();
        }
        return company;
    }

    public abstract String getCompanyName();
}
```

```java
public class Early extends Company {
    @Override
    public String getCompanyName() {
        return "Early";
    }
}
```

```java
    @Test
    void test1() {
        Company early = Company.getInstance("EffectiveJava.Item1.Early"); // 실제 경로
        assertThat(early.getCompanyName()).isEqualTo("Early"); // true

        Company kakao = Company.getInstance("item1s.kakao"); // 존재하지 않는 path
        assertThat(kakao.getCompanyName()).isEqualTo("KAKAO"); // 에러
    }
```

* * *

#### **정적 팩토리 메서드 단점**

#### **단점 1 : 상속을 하려면 public이나 protected 생성자가 필요하니 정적 팩토리 메서드만 제공하면 하위**

#### **클래스를 만들 수 없다.**

**생성자를 private으로 막고 자식 클래스인 Banana 클래스를 만들었다.**

**자식 클래스를 생성할 때는 부모 클래스를 먼저 호출하는데, 부모의 생성자가 private으로 지정되어 있어서 컴파일 에러가**

**발생한다. 따라서 이것을 해결하려면 부모 생성자를 public 또는 protected로 지정해주어야 한다.**

**단점이라고 이야기했지만, 사실 상속은 의존도를 높이므로 좋지 않다.**

**합성(Composition) 사용을 유도하여 오히려 장점으로 받아들일 수 있다.**

```java
public class Fruit {
    private Fruit() {};

    public static Banana createBanana() {
        return new Banana();
    }
}
public class Banana extends Fruit {
    public Banana() {
    	super();
    }
}
```

#### **단점 2 : 정적 팩토리 메서드는 프로그래머가 찾기 어렵다.**

**생성자처럼 API 설명에 명확히 드러나지 않으니 사용자는 정적 팩토리 메서드 방식 클래스를 인스턴스화할 방법을**

**알아내야 한다.**

* * *

#### **대표적인 메서드 명명 방식**

-   **from   
    → 매개 변수를 하나 받아서 해당 타입의 인스턴스를 반환하는 메서드로 주로 사용**  
    **→ Date date = Date.from(param);**
-   **of   
    → 여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메서드에서 주로 사용**  
    **→ List list = List.of(1,2,3);**
-   **valueOf  
    → from과 of와 비슷한 의미**  
    **→ Integer i = Integer.valueOf(10);**
-   **instance or getInstance   
    → 해당 요청에 맞는 인스턴스를 발화하는 메서드로 주로 사용**
-   **create or newInstance  
    → instance, getInstance와 같은 의미이나 매번 새로운 인스턴스 생성을 보장할 때 주로 사용**  
    **→ Object newArray = Array.newInstance(Integer.class, 10);**
-   **getType  
    → getInstance와 같으나 생성할 클래스가 아닌 다른 클래스에 팩토리 메서드를 정의할 때 사용.**  
    **→ Type은 팩토리 메서드가 반환할 객체의 타입**  
    **→ FileStore fileStore = Files.getFileStore(path);**
-   **newType  
    → newInstance와 같으나 생성할 클래스가 아닌 다른 클래스에 팩토리 메서드를 정의할 때 사용**   
    **→ BufferedReader bufferedReader = Files.newBufferedReader(path);**
-   **type  
    → getType, newTyp을 간결하게 사용할 때 사용**

* * *

#### **정리**

**정적 팩토리 메서드는 객체의 생성을 책임지고 객체의 생성방식도 관리할 수 있다.**

**위에서 설명한 바와 같이 많은 장점들을 얻을 수 있다.**

**평범한 경우에 public 생성자를 사용하되 이 방법이 모든 경우에 적절한 것은 아니며, 정적 팩토리 메서드를 사용해야  
할 상황인지를 고려해 보면 좋을 것 같다.**
