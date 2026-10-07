---
title: "[Effective Java] 아이템2 - 생성자에게 매개변수가 많다면 빌더를 고려하라"
date: 2023-11-22 09:52:30 +0900
categories: ["Language", "Effective Java"]
tags: ["effective java item2", "이펙티브자바 2장 객체 생성자 파괴", "이펙티브자바 item2", "이펙티브자바 아이템2"]
tistory_url: "https://aroma-bok.tistory.com/entry/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C2-%EC%83%9D%EC%84%B1%EC%9E%90%EC%97%90%EA%B2%8C-%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98%EA%B0%80-%EB%A7%8E%EB%8B%A4%EB%A9%B4-%EB%B9%8C%EB%8D%94%EB%A5%BC-%EA%B3%A0%EB%A0%A4%ED%95%98%EB%9D%BC"
---
**생성자와 정적 팩토리 메서드는 선택적 매개변수가 많을 때 적절히 대응하기 어렵다는 단점이 존재한다.**

#### **점층적 생성자 패턴**

-   **필수 매개변수를 받는 생성자 1개, 그리고 선택 매개변수를 하나씩 늘여가며 생성자를 만드는 패턴**

**필수 매개변수만 받는 생성자, 필수 매개변수와 선택 매개변수 1개를 받는 생성자, 선택 매개변수를 2개 받는 생성자...**  
**이런 형태로 선택 매개변수를 전부 다 받는 생성자까지 늘려가는 방식이다.**

```java
public class NutritionFacts {
    private final int servingSize; //(mL, 1회 제공량)  필수
    private final int servings;   //(회, 총 n회 제공량) 필수
    private final int calories;   //(1회 제공량당)     선택
    private final int fat;        //(g/1회 제공량)     선택
    private final int sodium;     //(mg/1회 제공량)    선택
    private final int carbohydrate; // (g/1회 제공량)  선택

    public NutritionFacts(int servingSize, int servings) {
        this(servingSize, servings, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories) {
        this(servingSize, servings, calories, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat) {
        this(servingSize, servings, calories,  fat, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium) {
        this(servingSize, servings, calories,  fat,  sodium, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate) {
        this.servingSize = servingSize;
        this.servings = servings;
        this.calories = calories;
        this.fat = fat;
        this.sodium = sodium;
        this.carbohydrate = carbohydrate;
    }
}
```

#### **점층적 생성자 패턴의 단점**

1.  **초기화하고 싶은 필드만 포함한 생성자가 없다면, 설정하길 원치 않는 필드까지 매개변수에 갑을 지정해야 한다.**
2.  **복잡하고 읽기 어렵다 그리고 클라이언트가 실수로 매개변수의 순서를 바꿔 건네줘도 컴파일러는 알아채지 못하고 결국 런타임시, 원치 않는 동작을 하게 된다.**
3.  **매개변수의 수가 많아질 경우 걷잡을 수 없다.**

**→ 매개변수가 많아지면 많아질수록 클라이언트 코드가 읽기 어려워진다.**

* * *

#### **자바빈즈 (JavaBeans) 패턴**

-   **매개변수가 없는 생성자로 객체를 만든 후, setter 메서드를 호출해 원하는 매개변수 값을 설정하는 방식**

```java
public class NutritionFactsForJavaBeans {
    private int servingSize = -1;
    private int servings = -1;
    private int calories = 0;
    private int fat = 0;
    private int sodium = 0;
    private int carbohydrate = 0;
    public NutritionFactsForJavaBeans() {

    }
    public void setServingSize(int servingSize) {
        this.servingSize = servingSize;
    }

    public void setServings(int servings) {
        this.servings = servings;
    }

    public void setCalories(int calories) {
        this.calories = calories;
    }

    public void setFat(int fat) {
        this.fat = fat;
    }

    public void setSodium(int sodium) {
        this.sodium = sodium;
    }

    public void setCarbohydrate(int carbohydrate) {
        this.carbohydrate = carbohydrate;
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        NutritionFactsForJavaBeans cocaCola = new NutritionFactsForJavaBeans();
        cocaCola.setServingSize(240);
        cocaCola.setServings(8);
        cocaCola.setCalories(100);
        cocaCola.setFat(0);
        cocaCola.setSodium(35);
        cocaCola.setCarbohydrate(27);
    }
}
```

#### **자바빈즈 (JavaBeans) 패턴 단점**

-   **객체 하나를 만들려면 메서드를 여러 개 호출해야 한다.**
-   **객체가 완성되기 전까지는 일관성이 무너진 상태에 놓이게 된다.**

**→ 클래스를 불변으로 만들 수 없으며, 스레드 안전성을 얻으려면 추가 작업을 해줘야 한다.**

* * *

#### **빌더 (Builder) 패턴**

-   **Builder를 이용해 필수 매개변수로 객체를 생성하고, 일종의 setter를 사용하여 선택 매개변수를 초기화한 뒤  
    build() 메서드를 호출하여 완전한 객체를 생성하는 패턴**

**클라이언트는 필요한 객체를 직접 만드는 대신, 필수 매개변수 만으로 생성자를 호출해 빌더 객체를 얻는다.**

**그런 다음 빌더 객체가 제공하는 일종의 setter 메서드들로 원하는 선택 매개변수들을 설정한다.**

**마지막으로 매개변수가 없는 build 메서드를 호출해 객체를 얻는다.**

**점층적 생성자 패턴의 안전성과 자바빈즈 패턴의 가독성을 겸비했다.**

**빌더는 생성할 클래 안에 정적 멤버 클래스로 만들어두는 게 보통이다. (Lombok으로 사용 가능)**

```java
public class NutritionFactsForBuilder {
    private final int servingSize; //(mL, 1회 제공량)  필수
    private final int servings;   //(회, 총 n회 제공량) 필수
    private final int calories;   //(1회 제공량당)     선택
    private final int fat;        //(g/1회 제공량)     선택
    private final int sodium;     //(mg/1회 제공량)    선택
    private final int carbohydrate; // (g/1회 제공량)  선택

    public NutritionFactsForBuilder(Builder builder) {
        servingSize = builder.servingSize;
        servings = builder.servings;
        calories = builder.calories;
        fat = builder.fat;
        sodium = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }

    public static class Builder {
        // 필수 매개변수
        private final int servingSize;
        private final int servings;

        // 선택 매개변수
        private int calories = 0;
        private int fat = 0;
        private int sodium = 0;
        private int carbohydrate = 0;

        // 필수 매개변수만을 담은 Builder 생성자
        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings = servings;
        }

        // 선택 매개변수의 setter, Builder 자신을 반환해 연쇄적으로 호출 가능
        public Builder calories(int val) {
            calories = val;
            return this;
        }
        public Builder fat(int val) {
            fat = val;
            return this;
        }
        public Builder sodium(int val) {
            calories = val;
            return this;
        }
        public Builder carbohydrate(int val) {
            carbohydrate = val;
            return this;
        }

        // build() 호출로 최종 불변 객체를 얻는다.
        public NutritionFactsForBuilder build() {
            return new NutritionFactsForBuilder(this);
        }
    }
}
```

**위 클래스는 불변이며, 모든 매개변수의 기본값을 한 곳에 모아 뒀다.**

**이 빌더의 setter 메서드는 빌더 자신을 반환하기 때문에 연쇄적으로 호출할 수 있다. (Method Chaining)**

```java
NutritionFactsForBuilder cola = new NutritionFactsForBuilder.Builder(240, 8)
        .calories(100).sodium(35).carbohydrate(30).build();
```

> **빌더(Builder) 패턴과 자바빈즈(JavaBeans) 패턴의 가장 큰 차이점은 '불변성'이다.**  
> **자바빈즈 패턴은 객체를 생성한 후, 값을 setter 메서드로 통해 넣는다.**  
> **그렇기 때문에 객체 사용도중 setter 메서드를 통해 값이 언제든지 변경될 수 있다.**  
> **반면에, 빌더 패턴은 객체 생성 전 값을 setter 메서를 통해 넣는다. 그리고 마지막에 build를 통해 객체를 생성한다. 그렇기 때문에 객체 사용 중에 값이 변경될 우려가 없으며 불변성과 안정성이 올라간다.  
> **  
> **당연하지만, 빌더패턴 사용 시에는 public setter 메서드를 선언해서는 안된다. (Builder를 통해 넣어야 한다.)**

**빌더패턴은 계층적으로 설계된 클래스와 함께 쓰기에 좋다**

-   **각 계층의 클래스에 관련 빌더를 멤버로 정의한다.**
-   **추상 클래스는 추상 빌더를 갖게 한다.**
-   **구체 클래스(Concrete Class)는 구체 빌더(Concrete Builder)를 갖게 한다.**

```java
public abstract class Pizza {
    
    public enum Topping {
        HAM, MUSHROOM, ONION, PEPPER, SUSAGE
    }
    
    final Set<Topping> toppings;
    
    Pizza(Builder<?> builder) {
        toppings = builder.toppings.clone();
    }
    
    abstract static class Builder<T extends Builder<T>> {
        private EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
        
        public T addTopping(Topping topping) {
            toppings.add(topping);
            return self();
        }
        abstract Pizza build();
        
        protected abstract T self();
    }
}
```

**Pizza.Builder 클래스는 재귀적 타입 한정을 이용하는 제네릭 타입이며, 추상 메서드인 self()를 더해 하위 클래스에서는**

**형변환하지 않고도 메서드 연쇄를 지원한다. 하위 클래스에서는 이 추상 메서드의 반환 값을 자기 자신을 주면 된다.**

**Pizza의 하위 클래스인 NyPizza와 CalzonePizza를 보며 빌더 패턴의 유연함을 확인해 보자.**

```java
public class NyPizza extends Pizza {
    public enum Size {
        SMALL, MEDIUM, LARGE
    }

    private final Size size; // 필수 매개변수

    NyPizza(Builder builder) {
        super(builder);
        size = builder.size;
    }

    public static class Builder extends Pizza.Builder<Builder> {

        private final Size size;

        public Builder(Size size) {
            this.size = size;
        }

        @Override
        NyPizza build() {
            return new NyPizza(this);
        }

        @Override
        protected Builder self() {
            return this;
        }
    }
}
```

```java
public class CalzonePizza extends Pizza {
    private final boolean sauceInside; // 선택 매개변수

    private CalzonePizza(Builder builder) {
        super(builder);
        sauceInside = builder.sauceInside;
    }

    public static class Builder extends Pizza.Builder<Builder> {
        private boolean sauceInside = false;

        public Builder sauceInside() {
            sauceInside = true;
            return this;
        }

        @Override
        CalzonePizza build() {
            return new CalzonePizza(this);
        }

        @Override
        protected Builder self() {
            return this;
        }
    }
}
```

**각 하위 클래스의 빌더가 정의한 build() 클래스는 구체 하위 클래스를 반환하고 있다. 하위 클래스의 메서드가**

**상위 클래스의 메서드가 반환한 타입이 아닌, 그 하위 타입을 반환하는 기능을 '공변 반환 타이핑'이라고 한다.**

**이 기능을 이용하면 클라리언트가 형변환에 신경 쓰지 않고도 빌더를 사용할 수 있다.**

```java
NyPizza nyPizza = new NyPizza.Builder(NyPizza.Size.SMALL)
        .addTopping(Pizza.Topping.SUSAGE)
        .addTopping(Pizza.Topping.ONION)
        .build();

CalzonePizza calzonePizza = new CalzonePizza.Builder()
        .addTopping(Pizza.Topping.HAM)
        .sauceInside()
        .build();
```

**빌더를 이용하면 가변인수 매개변수를 여러 개 사용할 수 있다. 실제로 addTopping 메서드가 위처럼 구현된다.**

**빌더 하나로 여러 객체를 순회하면서 만들 수 있고, 빌더에 넘기는 매개변수에 따라 다른 객체를 만들 수도 있다.**

**빌더(Builder) 패턴** **단점**

-   **빌더 객체를 생성해야 한다.**
-   **점층적 생성자 패턴보다는 코드가 장황해서 매개변수가 4개 이상은 되어야 값어치를 한다.**

**빌더(Builder) 패턴 핵심 정리**

-   **생성자나 정적 패터리가 처리해야 할 매개변수가 많다면 빌더 패턴을 선택하는 게 더 낫다.**
-   **매개변수 중 다수가 필수가 아니거나 같은 타입이면 특히 더 그렇다.**
-   **빌더는 점층적 생성자보다 클라이언트 코드를 읽고 쓰기가 훨씬 간결하고, 자바빈즈보다 안전하다.**

* * *

**Lombok을 이용한 @Builder**

-   **Lombok으로 @Builder 어노테이션을 붙이면 Builder 패턴을 생성해 준다.**
-   **setter 없이 필요한 매개변수 값을 set 한 후에 build 하여 thread-safe 하게 사용할 수 있다.**

```java
@Builder
public class NutritionFactsForAnnotation {
    private final int servingSize; //(mL, 1회 제공량)  필수
    private final int servings;   //(회, 총 n회 제공량) 필수
    private final int calories;   //(1회 제공량당)     선택
    private final int fat;        //(g/1회 제공량)     선택
    private final int sodium;     //(mg/1회 제공량)    선택
    private final int carbohydrate; // (g/1회 제공량)  선택
}
```

```java
NutritionFactsForAnnotation a = NutritionFactsForAnnotation.builder()
        .servings(10)
        .sodium(10)
        .fat(10)
        .carbohydrate(10)
        .build();
```
