---
title: "[Java] toString() vs valueOf() 차이 및 null 체크"
date: 2023-09-12 14:44:59 +0900
categories: ["Language", "Java"]
tags: ["tostring valueof null", "tostring valueof null 체크", "tostring valueof 차이", "tostring vs valueof"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-toString-vs-valueOf-%EC%B0%A8%EC%9D%B4-%EB%B0%8F-null-%EC%B2%B4%ED%81%AC"
---
**개발하는 과정에서 toString과 valueOf 차이를 정확하게 알아야 했던 시점이 있었다.**

**DTO를 만들고 테스트하는 과정에서 갑자기 NullPointerException이 발생했던 적이 있다. 그래서 그 원인을 확인하고**

**차이점을 정리하였다.**

* * *

**toString과 String.valueOf 둘 다 객체를 문자열로 변환하는 메서드로 자주 사용하는 메서드다.**

**두 메서드 간에는 몇 가지 중요한 차이점이 존재한다.**

#### **toString()**

-   **객체 자체에 정의된 메서드이며, Object 클래스에서 상속된다.**
-   **객체가 가지고 있는 내용을 문자열로 표현할 때, 해당 객체 클래스에서 오버라이딩하여 구현할 수 있다.**
-   **객체의 문자여을 표현을 반환하기 때문에, 객체가 null 인 경우에는 NullPointerException 발생**

```java
// 오버라이딩하여 원하는 출력 값을 나타낼 수 있다.

class Person {
    private String name;

    public Person(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "Person: " + name;
    }
}

public class Main {
    public static void main(String[] args) {
        Person person = new Person("Alice");
        String result = person.toString();
        System.out.println(result); // 출력: "Person: Alice"
    }
}
```

```java
// 객체가 null 값이면 toString 사용시, NullPointerException 발생
Object obj = null;
String str = obj.toString(); // NullPointerException 발생
```

* * *

#### **valueOf()**

-   **String 클래스의 정적 메서드인 valueOf는 다양한 데이터 타입 (기본 데이터 타입 및 객체)을 문자열로 반환**
-   **입력된 값에 대해 문자열 표현을 반환한다. 이 메서드는 null 값을 인자로 받을 수 있으며,**  
    **null 이 입력된 경우에는 null 문자열을 반환한다.**

```java
int number = 42;
String str1 = String.valueOf(number); // 정수를 문자열로 변환
System.out.println(str1); // 출력: "42"

double value = 3.14;
String str2 = String.valueOf(value); // 실수를 문자열로 변환
System.out.println(str2); // 출력: "3.14"
```

```java
// 객체가 null이면 null을 반환한다.
Object obj = null;
String str = String.valueOf(obj); // str 변수에는 "null" 문자열이 할당됨
```

* * *

**요약하면, String.valueOf() 메서드를 이용하면 null 처리에 대해서 별도의 예외처리가 필요하지 않다.**

**null 값이 들어오면 null 문자열을 반환하기 때문이다.**

**물론, toString() 메서드를 사용할 때에도 null 체크를 수행하여 NullPointerException을 방지할 수 있다.**

```java
Object obj = null;
String str;
if (obj != null) {
    str = obj.toString();
} else {
    str = "null"; // 또는 다른 기본값 할당
}
```

**하지만, String.valueOf() 메서드를 사용하면 이러한 null 체크 코드를 생략할 수 있어서**

**코드를 더 간결하게 작성할 수 있다.**
