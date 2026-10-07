---
title: "[Effective Java] 아이템9 - try-finally 대신 try-with-resources를 사용하라"
date: 2023-11-28 13:43:09 +0900
categories: ["Language", "Effective Java"]
tags: ["effective java item9", "effective java 아이템9", "이펙티브 자바 item9", "이펙티브 자바 아이템9"]
tistory_url: "https://aroma-bok.tistory.com/entry/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C9-try-finally-%EB%8C%80%EC%8B%A0-try-with-resources%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC"
---
**자바 라이브러리에는 close 메서드를 호출해 직접 닫아줘 하는 자원이 있다.**

**예를 들어, InputStream, OutputStream, java.sql.Connection 등**

**자원 닫기는 클라이언트가 놓치기 쉬워서 예측할 수 없는 성능 문제를 야기할 수 있다.**

**전통적으로 자원이 제대로 닫힘을 보장하는 수단으로 try-finally 가 사용되었다.**

**아래 코드처럼 close를 호출하게 되면 문제가 발생하지 않는다.**

**BufferedReader는 사용 중 IOException이 발생할 수 있는데, 만약 br.readLine() 메서드에서 Exception이 발생하게**

**되면 메서드는 종료되고 close는 호출되지 않고 스트림이** **메모리에 남아있게 된다.**

```java
public String inputString() throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    String result = br.readLine();
    br.close();
    return result;
}
```

**그래서 전통적으로 try-finally 문을 사용해서 close 처리를 해준다.**

**finally 블록은, try-catch 블록이 끝난 뒤 실행할 로직을 정의해 주는 블록이다.**

**따라서 Exception이 발생해도 close를 실행한다.**

```java
public String inputString2() throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    String result;

    try {
        result = br.readLine();    
    }finally {
        br.close();

    }
    return result;
}
```

**하지만, 그래도 여전히 실수를 저지를 가능성이 존재한다.**

**try-finally 방식은 자원이 둘 이상이면 코드가 너무 지저분해지고, 실수를 저지를 수 있다.**

```java
public void inputString3() throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    String result;

    try {
        BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));
        try {
            result = br.readLine();
            bw.write(result);
        }finally {
            bw.close();
        }
    }finally {
        br.close();
    }
}
```

**그리고 코드의 가독성 문제보다 더 큰 문제가 존재한다.**

**try 블록을 실행하던 도중 문제가 생겨 readLine이 정상적으로 실행되지 못하고 예외를 던지고**

**같은 이유로** **finally 블록이 close 메서드도 예외를 던지게 된다.**

**이때, finally 블록에서 터진 예외가 try 블록에서 생긴 예외를 집어삼켜서 finally 블록의 예외만 체크하게 된다.**

**try 블록에서 터진 예외로 인해 finally 블록 예외가 발생했음에도 불구하고 최초 원인인 예외를 체크하지 못하게 되는 것이다.** 

**물론 적절한 코드를 통해 최초 원인 예외를 체크할 수 있지만, 코드의 가독성이 안 좋아지기에 추천하지 않는 방법이다.**

* * *

#### **그렇다면 어떻게 해결할 수 있을까?**

**이러한 try-finally 방식의 단점을 보완하기 위해서 Java7부터는 try-with-resources가 도입되었다.**

**try-with-resources를 사용하기 위해서는 사용하는 자원이 AutoCloseable 인터페이스를 구현해야 한다.**

**try(...)에서 선언된 객체들에 대해서 try가 종료될 때 자동으로 자원을 해제해 주는 기능이다.**

```java
public String inputString5() throws IOException {
    String result;
    try(BufferedReader br = new BufferedReader(new InputStreamReader(System.in));) {
        result = br.readLine();
        return result;
    }
}
```

**이렇게 코드를 작성하면 가독성이 좋아지고, 예외가 발생했을 때 디버깅 하기에도 더 편리해졌다.**

**readLine, close 각 메서드에서 예외가 발생하는 경우, close 호출 시 발생하는 예외는 숨겨지고 readLine의 예외가 기록된다.**

**※ *close는 코드에서 보이지 않지만 AutoCloseable을 구현하였다면, try 구문이 종료될 때 객체의 close 메서드를 자동으로 호출한다.***

* * *

#### **참고**

**BufferedReader가 상속한 Reader 클래스를 타고 가면 아래처럼 AutoCloseable을 상속하고 있는 것을 확인할 수 있다.**

```java
public interface Closeable extends AutoCloseable
```
