---
title: "[Java] Generic 그리고 TypeReference"
date: 2024-08-22 15:10:39 +0900
categories: ["Language", "Java"]
tags: ["generic이란?", "java generic", "java typereference", "typereference", "자바 jackson typereference", "자바 json 역직렬화", "자바 제네릭", "자바 타입레퍼런스", "제네릭이란?"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-Generic-%EA%B7%B8%EB%A6%AC%EA%B3%A0-TypeReference"
---
**개발하는 과정에서 TypeReference를 사용하는 이유와 제네릭(Generic)에 대한 개념이 부족하다고 판단하여 정리한 글**

* * *

### **Generic**

**\- 데이터 타입(Data Type)을 일반화(Generalize)한다는 것을 의미**  
**\- 클래스나 메소드에서 사용할 내부 데이터 타입을 컴파일 시에 미리 지정하는 방법**  

* * *

#### **컴파일 시에 미리 타입 검사(Type Check)를 수행하면 다음과 같은 장점이 존재**

**1\. 클래스나 메소드 내부에서 사용되는 객체의 타입 안정성을 높일 수 있다.**  
**2. 반환값에 대한 타입 변환 및 타입 검사에 들어가는 노력을 줄여준다.**

* * *

  
  
**JDK 1.5 이전에는 여러 타입을 사용하는 대부분의 클래스나 메소드에서 인수나 반환값으로 Object 타입을  
사용했다.** **하지만, 이 경우에는 반환된 Object 객체를 다시 원하는 타입으로 변환해야 하며, 이때 오류가 발생할 가능성도 존재한다.** **JDK 1.5부터 도입된 제네릭(Generic)을 사용하면 컴파일 시에 미리 타입이 정해지므로, 타입 검사나 타입 변환과 같은** **번거로운 작업을 생략할 수 있게 되었다.**

```java
class MyArray<T> {

    T element;

    void setElement(T element) { this.element = element; }

    T getElement() { return element; }

}
```

  
**위의 예제에 사용된 'T'를 타입변수(Type Variable)라고 하며, 임의의 참조형 타입을 의미한다.**  
**꼭 'T'뿐만 아니라 어떠한 문자도 사용해도 상관없으며, 여러 개의 타입 변수는 쉼표(,)로 구분하여 명시할 수 있다.** **타입 변수는 클래스에서 뿐만 아니라 메소드의 매개변수나 반환값으로도 사용할 수 있다.**  
  
**위와 같이 선언된 제네릭 클래스(Generic Class)를 생성할 때에는 타입 변수자리에 사용할 실제 타입을 명시해야 한다.**

```java
MyArray<Integer> myArr = new MyArray<Integer>();
```

  
**MyArray 클래스에 사용된 타입 변수로 Integer 타입을 사용하는 예제이다.**  
  
**위처럼 제네릭 클래스(Generic Class)를 생성할 때, 사용할 실제 타입을 명시하면 내부적으로는 정의된 타입 변수가** **명시된 실제 타입으로 변환되어 처리된다.**

* * *

#### **참고**

**자바에서 타입 변수 자리에 사용할 실제 타입을 명시할 때, 기본타입을 바로 사용할 수 없다.**  
**이때는 위 예제의 Integer와 같이 래퍼(Wrapper) 클래스를 사용해야 한다.**

**\- 자바에서는 데이터 타입을 크게 2가지로 나눈다.**

#### **1\. 기본타입(Primitive Type)**

**1) 정수타입(byte, short, int, long)**  
**2) 실수타입(float, double)**  
**3) 논리타입(boolean)**  
**4) 문자타입(char)**

#### **2\. 참조타입(Reference Type)**

**1) 배열**  
**2) 열거타입**  
**3) 클래스**  
**4) 인터페이스**

#### **래퍼 클래스(Wrapper Class)**

**\- 기본타입(Primitive Type)을 객체로 다루기 위해서 사용하는 클래스**

**\- char타입과 int 타입을 제외한 나머지는 기본 타입의 첫 글자를 대문자로 바꾼 이름을 가진다.**

<table style="border-collapse: collapse; width: 79.8837%; height: 344px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 21px;"><td style="width: 50%; height: 21px; text-align: center;"><span style="color: #000000;"><b><span style="font-family: 'Noto Serif KR';">기본타입&nbsp;</span></b></span></td><td style="width: 50%; height: 21px; text-align: center;"><span style="color: #000000;"><b><span style="font-family: 'Noto Serif KR';">래퍼클래스</span></b></span></td></tr><tr style="height: 21px;"><td style="width: 50%; height: 21px; text-align: center;"><span style="color: #000000;"><b><span style="font-family: 'Noto Serif KR';">byte&nbsp;&nbsp;&nbsp;&nbsp;</span></b></span></td><td style="width: 50%; height: 21px; text-align: center;"><span style="color: #000000;"><b><span style="font-family: 'Noto Serif KR';">Byte</span></b></span></td></tr><tr style="height: 21px;"><td style="width: 50%; height: 21px; text-align: center;"><span style="color: #000000;"><b><span style="font-family: 'Noto Serif KR';">short&nbsp;&nbsp;&nbsp;</span></b></span></td><td style="width: 50%; height: 21px; text-align: center;"><span style="color: #000000;"><b><span style="font-family: 'Noto Serif KR';">Short</span></b></span></td></tr><tr style="height: 21px;"><td style="width: 50%; height: 21px; text-align: center;"><span style="color: #000000;"><b><span style="font-family: 'Noto Serif KR';">int&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span></b></span></td><td style="width: 50%; height: 21px; text-align: center;"><span style="color: #000000;"><b><span style="font-family: 'Noto Serif KR';">Integer</span></b></span></td></tr><tr style="height: 21px;"><td style="width: 50%; height: 21px; text-align: center;"><span style="color: #000000;"><b><span style="font-family: 'Noto Serif KR';">long&nbsp;&nbsp;&nbsp;&nbsp;</span></b></span></td><td style="width: 50%; height: 21px; text-align: center;"><span style="color: #000000;"><b><span style="font-family: 'Noto Serif KR';">Long</span></b></span></td></tr><tr style="height: 21px;"><td style="width: 50%; height: 21px; text-align: center;"><span style="color: #000000;"><b><span style="font-family: 'Noto Serif KR';">float&nbsp;&nbsp;&nbsp;</span></b></span></td><td style="width: 50%; height: 21px; text-align: center;"><span style="color: #000000;"><b><span style="font-family: 'Noto Serif KR';">Float</span></b></span></td></tr><tr style="height: 17px;"><td style="width: 50%; height: 17px; text-align: center;"><span style="color: #000000;"><b><span style="font-family: 'Noto Serif KR';">double&nbsp;&nbsp;</span></b></span></td><td style="width: 50%; height: 17px; text-align: center;"><span style="color: #000000;"><b><span style="font-family: 'Noto Serif KR';">Double</span></b></span></td></tr><tr style="height: 17px;"><td style="width: 50%; height: 17px; text-align: center;"><span style="color: #000000;"><b><span style="font-family: 'Noto Serif KR';">char&nbsp;&nbsp;&nbsp;&nbsp;</span></b></span></td><td style="width: 50%; height: 17px; text-align: center;"><span style="color: #000000;"><b><span style="font-family: 'Noto Serif KR';">Character</span></b></span></td></tr><tr style="height: 17px;"><td style="width: 50%; height: 17px; text-align: center;"><span style="color: #000000;"><b><span style="font-family: 'Noto Serif KR';">boolean&nbsp;</span></b></span></td><td style="width: 50%; height: 17px; text-align: center;"><span style="color: #000000;"><b><span style="font-family: 'Noto Serif KR';">Boolean</span></b></span></td></tr></tbody></table>

  
**박싱   : 기본타입 -> 래퍼클래스**

```java
Integer num = new Integer(1);
```

  
**언박싱 : 래퍼클래스 -> 기본타입**

```java
int newNum = num.intValue();
```

#### **사용하는 이유**

**1. 기본타입을 Object로 변환할 수 있다. 즉, 메소드에 전달된 인수를 수정하려는 경우 Obejct가 필요하다.**  
**2. java.util 패키지의 클래스는 객체만 처리하므로, 래퍼클래스는 이 경우에 도움이 된다.**  
**3. Collection 프레임 워크의 데이터 구조는 객체만 저장하므로, 래퍼클래스는 이 경우에 자동 박싱&언박싱을 통해 도움이 된다.**

* * *

### **제네릭(Generic)**

**\- 제네릭 타입에서는 매개변수화 타입(Parameterized Type)들을 정의**

\- **데이터 타입을 일반화하여 코드를 더 유연하고 재사용 가능하게 만드는 기능**

**\- 제네릭을 사용하면 클래스나 메서드를 작성할 때 특정 데이터 타입에 의존하지 않고, 다양한 타입으로 동작할 수 있게 설계할 수 있습니다.**

#### **Type Parameter(타입 매개변수)**

**\- '<>' 꺽쇠 괄호 내부에 있는 타입**

**\- List <String>의 String은 실 타입 매개변수**

#### **Raw Type**

**\- 제네릭 타입에서 타입 매개변수를 전혀 사용하지 않았을 때**  
**\- 안전성이 보장되지 않기에 사용하지 말자.**

```java
public class Example<T>  {
private T member;
  
  public void main(){
    Example<Integer> parameterType = new Example<>(1);
     Example rawType = new Example(1);
  }
}
```

**Example 클래스를 제네릭 타입으로 선언했지만, 예제에서는 제네릭을 사용하지 않고 생성했다.**  
**이럴 경우에 발생할 수 있는 문제점은 3가지다.**

#### **1. 표현력이 없다.**  
**2. 안정성이 없다.**  
**3. 컴파일 시기가 아닌 런타임 시기에 문제가 발생된다.**

#### **Type Eraser(타입 소거자)**

**\- 컴파일 타임에만 타입에 대한 제약 조건을 적용하고, 런타임에는 타입에 대한 정보를 소거한다.**

#### **Reifiable Type(실체화 타입)**

**\- 컴파일 단계에서 타입소거에 의해 지워지지 않는 타입 정보**  
**\- byte, int, short, long 등 원시타입**  
**\- Number, Integer 등 일반 클래스와 인터페이스 타입**  
**\- List, ArrayList, Map 등 자체 Raw Type**  
**\- List <?>, ArrayList <?> 등 비한정 와일드카드가 포함된 매개변수화 타입**

#### **Non-Reifiable Type(비실체화 타입)**

**\-** **타입소거에 의해서 타입 정보가 제거된 타입으로, 제네릭 타입 파라미터는 모두 제거된다.**  
**\- List <T>, List <Number>, ArrayList <String>, List <? extends Number>**

#### **Bounded Wildcard Type**

**\- 특정 타입으로 제한한다.**  
**\- 무공변(Invariant) : 오직 자기 타입만 허용**  
**\- 공변(Covariant, Upper bounded wildcard) : 구체적인 방향으로 타입 변환을 허용하는 것   
(자기 자신과 자식 객체만 허용), <? extends T>**  
**\- 반공변(Contravariant, Lower bounded wildcard) : 추상적인 방향으로의 타입 변환을 허용하는 것  
(자기 자신과 부모 객체만 허용), <? super T>**

* * *

### **참고**

**언제 super를 사용해야 하고, 언제 extends를 사용해야 하는지 헷갈릴 수 있다.**  
**그래서 '이펙티브 자바'에서는 'PECS'라는 공식을 만들었는데, 'Producer-Extends', 'Consumer-Super'의  
줄임말이다.**  

**즉, 컬렉션으로부터 와일드카드 타입의 객체를 꺼내서 생성하면(produce) extends를,  
갖고 있는 객체를 컬렉션에 사용(consumer)하여 더하면 super를 사용하라는 것이다.**

```java
void printCollection(Collection<? extends MyParent> c) { // 와일드카드 타입의 객체를 꺼내기에 extends
    for (MyParent e : c) {
        System.out.println(e);
    }
}

void addElement(Collection<? super MyParent> c) { // 갖고 있는 객체를 컬렉션에 사용하여 더하기에 super
    c.add(new MyParent());
}
```

* * *

#### **Recursive Type Bound**

**\- 타입 매개 변수가 자신을 포함하는 수식에 의해 한정될 수 있다.**

```java
public static <T> int countGreaterThan(T[] anArray, T elem) {
    int count = 0;
    for (T e : anArray)
        if (e > elem)  // compiler error
            ++count;
    return count;
}

public static <T extends Comparable<T>> int countGreaterThan(T[] anArray, T elem) {
    int count = 0;
    for (T e : anArray)
        if (e.compareTo(elem) > 0)
            ++count;
    return count;
}
```

**다음 코드에서 첫 번째 함수는 T가 비교 가능한지 모르기 때문에 컴파일 에러가 발생한다.**

#### **Subtyping in generics**

**\- 매개변수화 타입은 무공변이기 때문에 A <Integer> is a A <Number>가 성립하지 않는다.**  
**\- 이와 같은 문제를 해결하기 위해 'Wildcard Type'을 사용한다.**  
  

```java
public void addA(A<Number> a) {}
addA(new A<Integer>(3)) // X

public void addA(A<? extends Number> a) {}
addA(new A<Integer>(3)) // O
```

* * *

### **TypeReference**

 **- Jackson 라이브러리를 사용하여 JSON을 역직렬화할 때 제네릭 타입 정보를 보존하기 위해 사용**

```java
ObjectMapper objectMapper = new ObjectMapper();

String jsonString = "{\"name\":\"John\", \"age\":30, \"isEmployee\":true}";

// JSON 문자열을 Map<String, Object>로 변환
Map<String, Object> resultMap = objectMapper.readValue(jsonString, new TypeReference<Map<String, Object>>() {});
```

**참고한 곳**

**출처: [https://mangkyu.tistory.com/241](https://mangkyu.tistory.com/241) \[MangKyu's Diary:티스토리\]**  
**출처:[https://velog.io/@kny8092/TypeReference%EB%8A%94-%EC%99%9C-%ED%95%84%EC%9A%94%ED%95%9C%EA%B1%B8%EA%B9%8C](https://velog.io/@kny8092/TypeReference%EB%8A%94-%EC%99%9C-%ED%95%84%EC%9A%94%ED%95%9C%EA%B1%B8%EA%B9%8C)**
