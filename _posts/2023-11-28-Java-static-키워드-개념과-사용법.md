---
title: "[Java] static 키워드 개념과 사용법"
date: 2023-11-28 11:18:27 +0900
categories: ["Language", "Java"]
tags: ["java static", "static 개념", "static 개념 사용법", "자바 static", "자바 static 개념"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-static-%ED%82%A4%EC%9B%8C%EB%93%9C-%EA%B0%9C%EB%85%90%EA%B3%BC-%EC%82%AC%EC%9A%A9%EB%B2%95"
---
#### **Static (정적)**

-   **Static 키워드를 사용하여 Static 변수와 Static 메서드를 만들 수 있다.**
-   **Static 변수와 Static 메서드는 객체(인스턴스)에 소속된 멤버가 아니라, 클래스에 고정된 멤버이다.**
-   **클래스 로더가 클래스를 로딩해서 '메서드 메모리' 영역에 적재할 때 클래스별로 관리된다.**
-   **클래스의 로딩이 끝나는 즉시 바로 사용 가능하다.**

#### **Static 멤버 생성**

-   **Static 키워드를 사용해 생성된 Static 멤버(변수, 메서드)는 Heap 영역이 아닌, Static 영역에 할당된다.**
-   **Static 메모리에 할당된 메모리는 모든 객체가 공유하여 하나의 멤버를 어디서든지 참조 가능하다.**
-   **하지만, GC(Gabage Collector)의 관리 영역 밖에 존재하므로 static 영역에 있는 멤버들은 프로그램의**
-   **종료 시까지 메모리가 할당된 채로 존재한다.**
-   **그렇기에 static을 너무 남발하면 시스템에 악영향을 줄 수 있다.**

![](/assets/img/posts/262/1.png)

* * *

#### **Static 필드 사용 예시**

**number1, number2 2개의 인스턴스 변수를 생상하여서 각각 num, num2를 1씩 증가시켜 보는 테스트를 해봤다.**

```java
public class StaticExample {
    static int num = 0;
    int num2 = 0;
}

public static void main(String[] args) {
    StaticExample number1 = new StaticExample();
    StaticExample number2 = new StaticExample();

    number1.num++;
    number1.num2++;
    System.out.println(number2.num);
    System.out.println(number2.num2);
}
```

   
**결과는 static으로 선언된 num만 1이 증가하였다.**

```java
1
0
```

   
**이유는 뭘까?**  
   
**인스턴스 변수는 인스턴스가 생성될 때마다 생성되므로 인스턴스마다 각기 다른 값을 가지지만**  
**Static 변수는 모든 인스턴스가 하나의 저장공간을 공유하기에 항상 같은 값을 가지기에 나타나는 현상이다.**

* * *

#### **Static 메서드 사용 예시**

   
**Static 메서드는 클래스가 메모리에 올라갈 때 Static 메서드가 자동적으로 생성된다.**  
**그렇기 때문에, Static 메서드는 인스턴스를 생성하지 않아도 호출을 할 수 있다.**  
**Static 메서드는 유틸리티 함수를 만드는데 유용하게 사용된다.**

```java
public class StaticExample {
    static void print() {
        System.out.println("정적 메서드");
    }

    void print2() {
        System.out.println("인스턴스 메서드");
    }
}

public static void main(String[] args) {
    // 정적 메서드 사용 - 인스턴스를 생성하지 않고 사용 가능
    StaticExample.print();

    // 인스턴스 메서드 사용 - 인스턴스를 생성해야만 사용 가능
    StaticExample test = new StaticExample();
    test.print2();
}
```
