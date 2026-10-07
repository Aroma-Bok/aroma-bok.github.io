---
title: "[Java] Casting vs String.valueOf 차이점"
date: 2022-09-27 17:04:32 +0900
categories: ["Language", "Java"]
tags: ["casting", "string.valueof()", "string.valueof() vs casting 차이점"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-Casting-vs-StringvalueOf-%EC%B0%A8%EC%9D%B4%EC%A0%90"
---
**개발을 하다 보면 타입 변환을 하는 경우가 종종 생긴다. 어떤 건 Casting을 사용하고, 어떤 건 String.valueOf를 사용하고 있는데 차이가 뭘까라는 의문을 갖고 구글링을 통해 정리해보았다.**

**1\. String.valueOf()** 

-   **원시 타입이든 객체든 String으로 리턴**
-   **파라미터가 null이면 문자열 null을 만들어서 리턴**

**2\. Casting** 

-   **변환하려는 객체가 진짜 String 타입일 경우에만 캐스팅 후 리턴**
-   **대상이 null 이면 NullPointerException 발생**
-   **Object 값이 String이 아니면 ClassCastExcpetion 발생**

**아래 코드를 참조하면 조금 더 이해가 쉬울 것이다.**

```java
Object A = "AA";
String B = String.valueOf(A);

Object C = new Integer(42);
String D = (String) C; // will throw a ClassCastException
String E = String.valueOf(C);
```
