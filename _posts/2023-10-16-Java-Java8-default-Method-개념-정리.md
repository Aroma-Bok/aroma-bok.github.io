---
title: "[Java] Java8 default Method 개념 정리"
date: 2023-10-16 12:21:48 +0900
categories: ["Language", "Java"]
tags: ["default method", "interface default", "java", "java 기본메서드", "java8 default", "자바 인터페이스 default"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-Java8-default-Method-%EA%B0%9C%EB%85%90-%EC%A0%95%EB%A6%AC"
---
### **default Method - 기본 메소드**

-   **Java8에 도입**
-   **Interface에 메소드 선언뿐만 아니라, 구현체를 제공할 수 있는 방법**
-   **해당 Interface를 구현한 클래스를 깨트리지 않고, 새 기능을 추가하기 좋음.**  
    **즉, 유연성이 좋음.**
-   **원하는 하위 인터페이스, 클래에스만 제공할 수 있음.**

```java
public interface DefaultMethodEx {
    // 추상 메소드(Abstract Method)
    void printName();
}

------------------------------------

public class DefaultMethod implements DefaultMethodEx{
    @Override
    public void printName() {
        System.out.println("default Method 개념");
    }
}
```

```java
public static void main(String[] args) {
    SpringApplication.run(DesignPatternApplication.class, args);

    DefaultMethod defaultMethod = new DefaultMethod();
    defaultMethod.printName();
}
```

* * *

```java
public interface DefaultMethodEx {
    // 추상 메소드(Abstract Method)
    void printName();
    void printNameAdded(); // 새 기능 추가
}
```

**여기서 DefaultMethodEx를 구현한 클래스에 공통적으로 사용할 기능을 추가해야 하는 상황이 생겨**

**새로운 기능을 추가한다면 DefaultMethodEx를 구현한 모든 구현체에서 아래와 같은 에러가 발생한다.**

```java
Class 'DefaultMethod' must either be declared abstract or implement abstract method 'printNameAdded()' in 'DefaultMethodEx'
```

**구현체에 따로 구현을 해주지 않았기 때문에 발생하는 문제이다.**

**기본 메서드(default method)는 이러한 상황 때문에 생기게 되었다. 즉, 에러가 발생하지 않고 공통적으로 사용할 기능을**

**추가할 수 있는 방법이 기본 메서드(default method)를 활용하는 방법이다.**

```java
public static void main(String[] args) {
    SpringApplication.run(DesignPatternApplication.class, args);

    DefaultMethod defaultMethod = new DefaultMethod();
    defaultMethod.printName();
    defaultMethod.printNameAdded(); // default method 사용
}
```

-   **default method를 사용하면 구현체에서 따로 해당 메서드를 구현하지 않아도 사용 가능하다.**
-   **하지만, default method는 구현체가 모르게 추가된 기능이기 때문에 구현체에서는 알 수 있는 방법이 없다.**
-   **그렇기에 구현체에서는 default method의 기능, 반환 값 등을 모르기 때문에 문제가 발생할 수 있다.**  
    **즉, default로 제공되는 기능이 모든 구현체의 인스턴스에게 항상 제대로 동작한다는 보장이 없다.**
-   **@impleSpec라는 자바독 태그를 사용해서 문서화 작업을 하자.**

```java
public interface DefaultMethodEx {
    // 추상 메소드(Abstract Method)
    void printName();
    
    /**
     * @implSpec
     * 이 구현체는 getPringNameAdded() 으로 가져온 문자열을 소문자로 바꿔 출력한다.
     *
     */
    default void printNameAdded(String name) {
        System.out.println(getPringNameAdded(name).toLowerCase() + " : this is default method");
    }
    
    String getPringNameAdded(String name);
}
```

> **주의**  
> **\- 기본 메서드(default method)는 Object가 제공하는 기능(equals, hasCode)은 기본 메서드로 제공할 수 없다.**  
> **\- 기본 메서드(default method)는 사용자가 직접 정의한 인터페이스나 클래스에만 사용가능하며,  
>   외부 라이브러리 등에 추가하여 사용할 수 없다.**

* * *

#### **기본 메서드의 상속**

```java
public interface DefaultMethodEx {
    // 추상 메소드(Abstract Method)
    void printName();

    /**
     * @implSpec
     * 이 구현체는 getPringNameAdded() 으로 가져온 문자열을 소문자로 바꿔 출력한다.
     *
     */
    default void printNameAdded(String name) {
        System.out.println(getPringNameAdded(name).toLowerCase() + " : this is default method");
    }

    String getPringNameAdded(String name);
}

-----------------------------------------------------------------------------

public interface DefaultMethodExtend extends DefaultMethodEx{

    // DefaultMethodEx의 printNameAdded() 메소드를 defaulut로 두고 싶지 않을 때, 다시 추상 메서드로 변경할 수 있다.
    void printNameAdded();
}
```

**DefaultMethodEx 인터페이스에 있는 getPringNameAdded() 메소드를 default로 두고 싶지 않을 때, DefaultMethodExtend 인터페이스에서 해당 메소드를 다시 추상 메소드로 변경할 수 있다.**

#### **다이아몬드 문제**

-   **구현체가 상속받는 두 인터페이스 모두 동일한 기본 메서드(default method)가 있는 경우가 있다.**
-   **두 인터페이스가 동일한 이름을 가진 기본 메서드(default method)가 있는 경우에는 아래의 에러가 발생한다.**  
    **구현체가 동시에 두 인터페이스를 구현하려고 하기 때문에 컴파일 에러가 발생하는데, 이를 다이아몬드 문제라고 한다.**

```java
inherits unrelated defaults for printNameAdded(String) from types factoryMethod.DefaultMethodEx and factoryMethod.DefaultMethodEx2
```

```java
public interface DefaultMethodEx {
    /**
     * @implSpec
     * 이 구현체는 getPringNameAdded() 으로 가져온 문자열을 소문자로 바꿔 출력한다.
     *
     */
    default void printNameAdded(String name) {
        System.out.println(getPringNameAdded(name).toLowerCase() + " : this is default method");
    }
}

---------------------------------------

public interface DefaultMethodEx2 {

    /**
     * @implSpec
     * 이 구현체는 getPringNameAdded() 으로 가져온 문자열을 소문자로 바꿔 출력한다.
     *
     */
    default void printNameAdded(String name) {
        System.out.println(getPringNameAdded(name).toLowerCase() + " : this is default method");
    }
}

---------------------------------------

public class DefaultMethodExtend2 implements DefaultMethodEx, DefaultMethodEx2{

    // 다이아몬드 문제 해결을 위해서, 직접 Override를 해준다.
    @Override
    public void printNameAdded(String name) {
        DefaultMethodEx.super.printNameAdded(name);
    }

}
```
