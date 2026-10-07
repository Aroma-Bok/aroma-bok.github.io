---
title: "[Java] final 키워드 정리 (fianl 변수, 메서드, 클래스)"
date: 2023-11-28 12:17:00 +0900
categories: ["Language", "Java"]
tags: ["final", "final 불변성", "final 선언", "java final"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-final-%ED%82%A4%EC%9B%8C%EB%93%9C-%EC%A0%95%EB%A6%AC-fianl-%EB%B3%80%EC%88%98-%EB%A9%94%EC%84%9C%EB%93%9C-%ED%81%B4%EB%9E%98%EC%8A%A4"
---
**final은 변수, 메서드, 클래스에서 선언이 가능하다.**

**변수에 final을 선언하면, 해당 변수는 더 이상 '재할당'이 불가능하다.** 

**메서드에 선언하면, 해당 메서드는 '오버라이드(재정의)'가 불가능하다.**

**그리고 클래스에 선언하면 해당 클래스는 '상속'이 불가능하다. 즉, 부모 클래스가 될 수 없다.**

**final을 키워드를 사용하면, '어디에서 재할당은 되지 않았는지', '값이 변하는 상황을 막기 위해 검증 로직을 추가해야 하는지' 등 불안감에서 벗어나 다른 곳에 더 집중할 수 있게 도와준다.**

* * *

#### **final 변수 (final variable)**

**final 키워드를 사용한 변수에 초기값을 설정하는 방식은 2가지 있다.**

-   **클래스 필드에 선언 및 초기화**
-   **생성자에서 초기화**

**final이 선언되면 변수는 상수가 된다. 상수는 '변하지 않는 수'를 의미하며 상수로 선언한 변수는 값을 변경할 수 없다.**

```java
public class Service {
    public static void main(String[] args) {
        final int value = 2;
        final Person person = new Person("A", 18);

        // final 변수 수정 시, 컴파일 에러
//        value = 3;

        person.setAge(30);
        person.setName("B");

        // final 변수 수정 시, 컴파일 에러
//        person = new Person("B", 30);
        System.out.println("final 변수 수정 후 : " + person);
    }
}

@Getter
@Setter
@AllArgsConstructor
@NoArgsConstructor
public class Person {
    private String name;
    private int age;
}
```

**결과적으로 기본형 변수는 값을 수정하지 못하고, 참조형 변수는 그 객체 내부의 값은 변경할 수 있지만,**

**객체를 변경하지 못한다.**

```java
java: cannot assign a value to final variable value
```

**이 변수들은 각각 값을 가지는데 기본형 변수는 내부에 값을 가지고, 참조형 변수는 객체의 주소를 가진다.**

**따라서 참조형 변수는 객체에 있는 값을 알 수 있는 것이다.**

**기본형 변수라면 값을 변경하지 못하고, 참조형 변수라면 가리키는 객체를 변경하지 못한다.**

**참조형 변수는 최초 참조하는 객체 이외의 다른 객체를 참조할 수 없다.**  
**변수가 다른 객체를 참조하도록 바꿀 수는 없지만, 참조된 객체의 메서드를 통한 객체 자체의 값은 바꿀 수 있다.**  
**즉, *final 변수로 만들었다고 객체 자체가 불변(Immutable)이 되는 것은 아니다*.**

**그리고 메서드의 파라미터로 final을 선언하면 메서드 내부에서 인자를 변경할 수 없도록 한다.**

```java
    public void test1(final String a) {
        a = "test";
        // Cannot assign a value to final variable 'a'
    }
```

> **final과 static 키워드를 함께 사용하여 private(public) static final 형태로 변수를 많이 선언하는 것을 볼 수 있다.**  
> **static을 사용하면 컴파일 타임에 메모리 할당을 단 한번 하기 때문에 효율성 측면에서 사용하는 것이 좋다.  
>   
> static에 조금 더 알고 싶다면 [여기](https://aroma-bok.tistory.com/entry/Java-static-%ED%82%A4%EC%9B%8C%EB%93%9C-%EA%B0%9C%EB%85%90%EA%B3%BC-%EC%82%AC%EC%9A%A9%EB%B2%95)**

* * *

#### **final 메서드 (final Method)**

**상속된 자식 클래스에서 '오버라이딩 (재정의)' 할 수 없다.**

**주로 코어 부분에서 변경을 원치 않는 메서드를 명시할 때 사용한다.**

```java
public class FinalMethodTest {
    String a = "Test";
    public final void test() {
        System.out.println(a);
    }
}

public class ExampleClass extends FinalMethodTest {
    @Override
    public final void test() {
        System.out.println("test");
    }
    // 재정의 불가
    //'test()' cannot override 'test()' in 'EffectiveJava.finalExam.FinalMethodTest';
    //overridden method is final
}
```

* * *

#### **final 클래스 (final Class)**

**클래스를 final을 선언해 작성하면 그 내용을 수정할 수 없으므로 상속을 할 수 없다.**

**변수나 메서드를 재정의하면 기능이 정상적으로 동작하지 않는 클래스에 선언해 사용한다.**

```java
public final class FinalClassTest {
}

public class ExampleClass extends FinalClassTest {
	//Cannot inherit from final 'EffectiveJava.finalExam.FinalClassTest'
}
```

* * *

**final은 불변을 의미할까?**

**final은 완벽한 불변성을 의미하지 않는다.**

**final은 해당 변수의 재할당만 막아줄 뿐, 참조하고 있는 개체 내부의 상태가 변하지 않았음을 보장해주지는 않는다.**

```java
public static void main(String[] args) {
    final List<Integer> list = new ArrayList<>(Arrays.asList(1,2,3));

    list.add(4);
    System.out.println(list); // [1, 2, 3, 4]
}
```

**final을 선언하였지만, 내부의 값이 변경되는 것을 확인할 수 있다. final 키워드는 불변성을 지키고자 할 때 사용이 되지만,**

**final 단독으로는 완벽한 불변성을 지킬 수 없다는 것을 알 수 있다.**
