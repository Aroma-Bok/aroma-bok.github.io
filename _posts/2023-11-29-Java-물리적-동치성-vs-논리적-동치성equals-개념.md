---
title: "[Java] 물리적 동치성(==) vs 논리적 동치성(equals) 개념"
date: 2023-11-29 08:47:08 +0900
categories: ["Language", "Java"]
tags: ["equals vs ==", "java ==", "java equals", "java equals == 차이점", "java equals vs ==", "물리적 동치성(==) vs 논리적 동치성(equals) 개념", "물리적 동치성(==) 논리적 동치성(equals) 개념", "물리적 동치성과 논리적 동치성"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-%EB%AC%BC%EB%A6%AC%EC%A0%81-%EB%8F%99%EC%B9%98%EC%84%B1-vs-%EB%85%BC%EB%A6%AC%EC%A0%81-%EB%8F%99%EC%B9%98%EC%84%B1equals-%EA%B0%9C%EB%85%90"
---
#### **물리적 동치성 (==)**

**→ 메모리에 저장된 변수가 가지는 값(주소)이 서로 같은지 비교하는 것**

**→ 원시타입(Primitive Type)의 객체에 대해서는 값 비교가 가능하고, 참조타입(Reference Type)에 대해서는 주소 비교**

> **참조타입(Reference Type) 변수는 참조하는 객체의 주소 값을 가지고 있고,**  
> **원시타입(Primitive Type) 변수는 기본 타입의 값을 가지고 있다.**

* * *

#### **논리적 동치성 (equals)**

**→ 참조 타입(Reference Type) 변수를 비교하는 것**

**→ 비교할 값을 정하고, 값을 비교하여 두 객체가 서로 동등(갖고 있는 값이 같은지)하다면, "논리적으로 같다"라고 표현**

**→ Object 클래스의 equals()는 물리적 동치성(==)과 동일한 기능**

**String 클래스는 Object 클래스의 equals()를 오버라이딩하여 equals()를 '논리적 동치성'으로 사용하는 것이다.**

```java
// String 클래스가 아닌 일반 Object 클래스의 equals
StringBuilder sb1 = new StringBuilder("test");
StringBuilder sb2 = new StringBuilder("test");
System.out.println(sb1 == sb2);      //false
System.out.println(sb1.equals(sb2)); //false
```

**sb1, sb2는 new 연산자를 이용해 생성함으로써 서로 다른 객체를 참조하고 있다.**

**그리고 String 클래스가 아니라서, Object의 equals() 기능(==와 같은 기능)을 사용한다.**

**그렇기 때문에, 물리적 동치성(==)으로 작동해 false로 결과가 나온 것이다.**

> **※ 모든 클래스는 Object를 상속받는데 equals, hashCode, toString, clone, finalize를 재정의 할 수 있다.**

* * *

#### **String의 물리적 동치성 (==)과 논리적 동치성 (equals())**

**→ 위에서도 언급했지만, String 클래스는 Object의 equals()를 오버라이딩하여 '논리적 동치성' 기능으로 사용**

  
**String 변수를 생성하는 방법은 2가지가 있다.**

1.  **리터럴을 이용하는 방식**
2.  **new 연산자를 이용한 방식**

**이 2가지 방식에는 큰 차이점이 있다.**  
  
**'리터럴 이용 방식'은 String Constant Pool이라는 영역에 존재하게 된다.**

**String Constant Pool 영역에 만들어진 하나의 객체를 참조한다. (String Constant Pool 개념을 모른다면 [여기](https://aroma-bok.tistory.com/entry/Java-String-Constant-Pool-String-Pool-%EA%B0%9C%EB%85%90))**

  
**'new 연산자 이용 방식'은 Heap 영역에 존재하게 된다.**

**예시코드를 보고 확인해 보자.**

```java
public static void main(String[] args) {
    // 리터럴을 이용하여 생성 : String Constant Pool 영역
    String str1 = "test";
    String str2 = "test";

    // new 연산자를 이용하여 생성 : Heap 영역
    String str3 = new String("test");
    String str4 = new String("test");

    System.out.println(str1 == str2);      //true
    System.out.println(str1.equals(str2)); //true
    System.out.println(str3 == str4);	   //false
    System.out.println(str3.equals(str4)); //true
}
```

**str1, str2는 String Constant Pool 영역에 존재하는 객체를 참조한다. 같은 객체를 참조함으로써**

**물리적 동치성(==)과 논리적 동치성(equalse) 결과가 true이다.**

**반면에, str3, str4는 new 연산자를 이용해 생성함으로써 서로 다른 객체를 참조하고 있다.**

**그래서 물리적 동치성(==)은 false, 논리적 동치성(equals)은 true가 나온 것을 확인할 수 있다.**
