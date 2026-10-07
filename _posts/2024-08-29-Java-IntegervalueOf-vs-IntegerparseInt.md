---
title: "[Java] Integer.valueOf() vs Integer.parseInt()"
date: 2024-08-29 12:20:20 +0900
categories: ["Language", "Java"]
tags: ["integer.parseint()", "integer.valueof()", "integer.valueof() vs integer.parseint()"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-IntegervalueOf-vs-IntegerparseInt"
---
**갑자기 궁금해져서 정리해 보았다.**

* * *

### **Integer.valueOf() vs Integer.parseInt()**

**\-> 모두 문자열을 정수로 변환하는 역할을 하지만, 둘 사이에는 몇 가지 중요한 차이점이 있다.**

* * *

### **Integer.valueOf(String s)**

**\-> 반환타입 : Integer**  
**\-> 값을 반환할 때는 새 객체를 생성하지 않고 이미 캐싱된 객체를 반환**  
  

```java
String str = "123";
Integer num = Integer.valueOf(str);
```

  
**\-> Integer 객체가 필요할 때 사용**

* * *

### **Integer.parseInt(String s)**

**\-> 반환타입 : int**  
**\-> int 기본형을 반환하기 때문에 캐싱이나 객체 생성과 관련이 없다**

```java
String str = "123";
int num = Integer.parseInt(str);
```

  
**\-> 기본형 int가 필요할 때 사용**

* * *

  
**Integer.valueOf()**

**\-> Integer 객체를 반환, 캐싱을 통해 메모리 효율성을 높일 수 있아. 객체가 필요한 경우에 사용.**

  
**Integer.parseInt()**

**\->기본형 int를 반환, 객체 생성과 관련된 오버헤드가 없다. 숫자 연산이나 메모리 효율이 중요할 때 사용**  
  
**두 메서드는 기능적으로 유사하지만, 반환 타입과 사용 목적에 따라 적절한 메서드를 선택하는 것이 중요**
