---
title: "[Java] 얕은 복사 (Shallow Copy) vs 깊은 복사 (Deep Copy)"
date: 2023-10-19 10:51:42 +0900
categories: ["Language", "Java"]
tags: ["deep copy vs shallow copy", "java 깊은 복사", "java 얕은복사", "얕은 복사 vs 깊은 복사", "얕은 복사 깊은 복사"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-%EC%96%95%EC%9D%80-%EB%B3%B5%EC%82%AC-Shallow-Copy-vs-%EA%B9%8A%EC%9D%80-%EB%B3%B5%EC%82%AC-Deep-Copy"
---
#### **얕은 복사(Shallow Copy)** 

-   **실제 값이 아닌, 값을 가리키는 '주소'를 복사해 같은 객체를 가리키는 것을 뜻 한다.**
-   **원본 객체에 대해서 새로운 객체를 만들고 원본 객체를 참조한다.**
-   **call-by-reference와 유사한 개념**

-   **아래와 같이 Person 클래스가 있다.**

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Person {
    private String name;
    private int age;
}
```

-   **해당 클래스를 통해 인스턴스를 만들었고 아래와 같이 복사했다.**

```java
public class CopyTest {

    @Test
    void copyTest1() {
        Person person = new Person("Ramos", 33);
        Person copyPerson = person;

        copyPerson.setName("Messi");

        System.out.println(person.getName());
        System.out.println(copyPerson.getName());
        System.out.println(System.identityHashCode(person));
        System.out.println(System.identityHashCode(copyPerson));
    }
}
```

-   **의도한 결과는 새로운 인스턴스만 Messi로 변경하는 것인데, 기존의 인스턴스도 같이 변경되었다.**

```java
Messi
Messi
464887938
464887938
```

> **이유가 뭘까?**  
>   
> **→ 이유는 'copyPerson = person'과 같은 대입이 실제 값이 아닌 주소를 대입하는 것이기 때문이다.**  
>   
> **Person 인스턴스는 Heap 영역에 생성되고 인스턴스를 가리키는 pserson 변수가 Stack 영역에 생성된다.**  
> **이때, copyPerson 변수가 person 변수를 얕은 복사(Shallow Copy)를 하면 주소값의 Person 인스턴스를  
> 가리키게 된다.**  
>   
> **즉, setName()을 통해서 값을 변경하면 주소값의 인스턴스 값도 변경된다.**

* * *

#### **깊은 복사 (Deep Copy)**

-   **주소값이 아닌 실제 값을 복사하는 것을 뜻한다.**
-   **원본객체에 대해서 새로운 객체를 만들고 원본 객체로부터 독릭접인 객체를 생성**
-   **call-by-values와 유사한 개념**

**1\. Cloneable 인터페이스 구현**

-   **Cloneable 인터페이스의 clone() 메소드를 구현하여 깊은 복사 진행하는 방식**

**Cloneable 인터페이스 역할**

-   **Cloneable 인터페이스는 상속받은 클래스가 복제해도 되는 클래스임을 명시하는 용도의 믹스인 인터페이스**
-   **Cloneable 인터페이스 역할은 Object의 clone의 동작 방식을 결정**
-   **Cloneable 인터페이스를 상속받지 않고 clone 메소드를 호출하였다면 ' CloneNotSupportedExcetion'을 던진다.**
-   **아무것도 선언되어 있지 않은 빈 인터페이스**

> **참고**  
> **믹스인 : 클래스가 본인의 기능 이외에 추가로 구현할 수 있는 자료형으로, 어떤 선택적 기능을 제공한다는 사실을  
> 선언하기 위해 쓰인다**

-   **복제할 객체의 클래스에 Cloneable 인터페이스를 구현한다.**

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Person implements Cloneable{
    private String name;
    private int age;

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```

-   **Reflection을 통해서 protected로 선언된 clone 메서드 호출**

```java
    @Test
    void copyTest2() {
        Person person = new Person("Ramos", 33);
        Person copyPerson = ReflectionTestUtils.invokeMethod(person,"clone");

        copyPerson.setName("Messi");

        System.out.println(person.getName());
        System.out.println(copyPerson.getName());
        System.out.println(System.identityHashCode(person));
        System.out.println(System.identityHashCode(copyPerson));
        assertThat(person).isEqualTo(copyPerson);
    }
```

-   **의도한 대로 새로운 객체를 생성하고 값 변경**

```java
Ramos
Messi
1030684756
1348453796
```

> **참고**  
>   
> **기본 원칙은 '복제 기능은 생성자와 팩토리를 이용하는 게 가장 좋다'라고 한다.**  
> **단, 배열만은 clone 메서드 방식이 가장 깔끔하다.**

* * *

**2\. 생성자, 팩토리 이용**

-   **복사 생성자와 복사 팩토리 메소드를 클래스에 추가**

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Person {
    private String name;
    private int age;

    // 복사 생성자
    public Person(Person original) {
        this.name = original.name;
        this.age = original.age;
    }

    // 복사 팩토리
    public static Person copy(Person original) {
        Person copyPerson = new Person();
        copyPerson.name = original.name;
        copyPerson.age = original.age;
        return copyPerson;
    }
}
```

-   **Reflection을 통해서 protected로 선언된 clone 메서드 호출**

```java
    @Test
    void copyTest3() {
        Person person = new Person("Ramos", 33);
        Person copyPersonConstructor = new Person(person);
        Person copyPersonFactory = ReflectionTestUtils.invokeMethod(person,"copy",person);

        copyPersonConstructor.setName("Messi");
        copyPersonFactory.setName("Messi");

        System.out.println(person.getName());
        System.out.println(copyPersonConstructor.getName());
        System.out.println(copyPersonFactory.getName());
        System.out.println(System.identityHashCode(person));
        System.out.println(System.identityHashCode(copyPersonConstructor));
        System.out.println(System.identityHashCode(copyPersonFactory));
    }
```

-   **원본 객체는 그대로 유지되고 새로운 객체들이 생성된 것을 확인할 수 있다.**

```java
Ramos
Messi
Messi
1029472813
1866875501
1936722816
```

> **참고**  
>   
> **깊은 복사(Deep Copy)는 Heep 영역의 같은 인스턴스를 가리키지 않는다.  
> Heep 영역에 새로운 인스턴스를 생성하고 해당 인스턴스에 값을 복사하는 방식으로 동작한다.**  
> **즉, 복제된 값을 변경해도 같은 인스턴스를 가리키는 것이 아니라서 원본 객체가 변경되지 않는다.**

* * *

<table style="border-collapse: collapse; width: 100%; height: 194px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 20px;"><td style="width: 15%; height: 20px;">&nbsp;</td><td style="width: 35%; height: 20px;"><span style="font-family: 'Noto Serif KR';"><b>장점</b></span></td><td style="width: 50%; height: 20px;"><span style="font-family: 'Noto Serif KR';"><b>단점</b></span></td></tr><tr style="height: 72px;"><td style="width: 15%; height: 72px;"><span style="font-family: 'Noto Serif KR';"><b>얕은 복사</b></span><br><span style="font-family: 'Noto Serif KR';"><b>(Shallow Copy)</b></span></td><td style="width: 35%; height: 72px;"><span style="font-family: 'Noto Serif KR';"><b>빠르고 간결하다.</b></span></td><td style="width: 50%; height: 72px;"><span style="font-family: 'Noto Serif KR';"><b>원본 객체가 수정되는 경우 복사 객체가 원본 객체와 동일하게 변동이 생긴다. 즉, 원본 객체에 종속족이다.</b></span></td></tr><tr style="height: 102px;"><td style="width: 15%; height: 102px;"><span style="font-family: 'Noto Serif KR';"><b>깊은 복사</b></span><br><span style="font-family: 'Noto Serif KR';"><b>(Deep Copy)</b></span></td><td style="width: 35%; height: 102px;"><span style="font-family: 'Noto Serif KR';"><b>원본 객체에 독립된 새로운 객체로 다형성을 부여하여 사용하거나 재정의 가능</b></span></td><td style="width: 50%; height: 102px;"><span style="font-family: 'Noto Serif KR';"><b>모든 인스턴스 값을 갖고 오기 때문에 얕은 복사에 비해 상대적으로 느리고 복잡하다.</b></span></td></tr></tbody></table>
