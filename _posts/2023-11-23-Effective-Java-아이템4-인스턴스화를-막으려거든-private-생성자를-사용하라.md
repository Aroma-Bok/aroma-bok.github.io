---
title: "[Effective Java] 아이템4 - 인스턴스화를 막으려거든 private 생성자를 사용하라"
date: 2023-11-23 10:41:52 +0900
categories: ["Language", "Effective Java"]
tags: ["effective java item4", "effective java 아이템4", "이펙티브자바 item4", "이펙티브자바 아이템4"]
tistory_url: "https://aroma-bok.tistory.com/entry/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C4-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94%EB%A5%BC-%EB%A7%89%EC%9C%BC%EB%A0%A4%EA%B1%B0%EB%93%A0-private-%EC%83%9D%EC%84%B1%EC%9E%90%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC"
---
#### **클래스를 구현하다 보면 인스턴스를 생성할 필요가 없는 경우가 가끔 있다.**

**물론, static 메서드와 static 필드만을 담은 유틸리티 클래스는 객체지향과 거리가 멀지만, 쓰임새가 있다.**

```java
Java에서의 유틸리티 클래스의 예시 : java.lang.Math, java.util.Arrays, java.util.Collections
```

**정적 메서드만 담은 정적 유틸리티 클래스가 그런 경우이다. 보통 정적 유틸리티 클래스는 인스턴스를 생성해서**

**사용하도록 설계한 클래스가 아니다.**

```java
public class UtilityClass {
    public static String hello() {
        return "Hello";
    }
}
```

**인스턴스를 생성하여 메서드를 호출하는 게 문법적으로 잘못된 건 아니지만, 바로 hello() 메서드를 호출할 수 있음에도**

**불필요하게 인스턴스를 생성하여 hello() 메서드가 정적 메서드인지, 인스턴스 메서드인지 헷갈리게 만드는 코드이다.**

**그래서 *인스턴스 생성을 방지하 자라는 게 이번 아이템 4의 주제*이다.**

```java
    @Test
    void test1() {
        System.out.println(UtilityClass.hello());       // 인스턴스 생성하지 않고 사용

        UtilityClass utilityClass = new UtilityClass(); // 인스턴스 생성 후 사용
        System.out.println(utilityClass.hello());
    }
```

* * *

#### **반론**

**클래스에 abstract를 추가하면 추상클래스로 생성되기 때문에 인스턴스 생성을 막을 수 있지 않냐고 생각할 수 있다.**

```java
public abstract class UtilityClassAbstract {
    public static String hello() {
        return "Hello";
    }
}
```

**하지만 추상클래스도 인스턴스로 생성이 될 수 있다.**

```java
public abstract class UtilityClassAbstract {
    public static String hello() {
        return "Hello";
    }
}

public class DefaultClass extends UtilityClassAbstract{
}
```

**UtilityClassAbstract를 상속하는 DefaultClass의 인스턴스를 생성할 때, 부모인 UtilityClassAbstract 클래스의**

**생성자를 호출해 결국 인스턴스를 생성하게 된다.**

**즉, 추상클래스로 인스턴스 생성을 완전히 막을 수 없고, abstract라는 키워드 때문에 이 클래스는 상속용도로** 

**쓰이는 거라고 착각을 일으킬 수 있다. 그래서 기존의 정적 유틸리티 메서드로 쓰려고 하는 의도와 맞지 않다.**

```java
    @Test
    void test2() {
        // 상속받은 클래스는 인스턴스를 생성할 때 부모 클래스의 생성자를 호출하게 되어 있다.
        // 즉, 추상클래스인 부모도 결국 인스턴스가 생성된다.
        DefaultClass defaultClass = new DefaultClass();
        System.out.println(defaultClass.hello());
    }
```

* * *

#### **그렇다면 어떻게 해야 할까?**

**기본 생성자의 접근제한자를 private으로 변경하여 인스턴스 생성, 상속을 방지하자**

**abstract 키워드로 추상클래스를 만들지 않고, private 생성자로 해당 클래스 밖에서 인스턴스를 만들 수 없게 할 수 있다.**

**하지만, 내부에서는 생성할 수 있다.**

```java
public class UtilityClass2 {
    
    private UtilityClass2() {
        
    };
    
    public static String hello() {
        return "Hello";
    }
}
```

**만약, 내부에서까지 인스턴스 생성을 방지하려면 private 생성자를 호출할 때 AssertionError를 던지도록 해야 한다.**

**AssertionError는 try-catch를 처리하도록 하는 예외는 아니고 발생되면 안 되는 상황인데 혹시 발생되게 되면 무조건 예외가 아니라 에러를 던진다. 결론적으로 private 생성자에 AssertionError를 던지도록해서 내부에서도 인스턴스 생성을**

**방지할 수 있다.**

```java
public class UtilityClass2 {
    
    private UtilityClass2() {
        throw new AssertionError();
    };
    
    public static String hello() {
        return "Hello";
    }
}
```

> **AssertionError**  
> **→ 런타임 오류로 인식되며, 코드에서 의도한 것과 다른 결과가 발생하는 문제를 식별하는데 도움이 된다.**

**추가적으로 덧 붙이자면, AssertionError를 던지도록 코드를 작성하면, 다른 사람들이 이해하는 데 있어서**

**어려움이 있을 수 있다. 굳이 생성자를 만들면서 못쓰게 하는 이유에 대해서 이해하기 어려울 수 있다.**

**그래서 아래와 같이 주석으로 문서화하는 것을 추천한다.**

```java
public class UtilityClass2 {
    
   /**
    *   이 클래스는 인스턴스를 만들 수 없다. 
    */
    private UtilityClass2() {
        throw new AssertionError();
    };
    
    public static String hello() {
        return "Hello";
    }
}
```
