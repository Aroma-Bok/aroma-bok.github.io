---
title: "[Java] String Constant Pool (String Pool) 개념"
date: 2023-11-23 13:11:03 +0900
categories: ["Language", "Java"]
tags: ["java string pool", "string constant pool", "string pool", "자바 스트링 풀"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-String-Constant-Pool-String-Pool-%EA%B0%9C%EB%85%90"
---
#### **String Constant Pool**

**→ Java에서 문자열 리터럴을 저장하는 독립된 영역**  
**→ Java6까지 String Constant Pool의 위치는 Perm 영역이었으며, Java7에서는 OOM 문제로 인하여 Heap 영역으로**  
**변경되었다고 한다.**  
**→ Perm 영역은 고정된 사이즈이며 Runtime에 사이즈가 확장되지 않는다. Java6 까지는 String의 intern() 메서드를 호출하는 것은 OutOfMemoryException을 발생시킬 수 있고 그 부분을 컨트롤할 수 없었기 때문에 거의 사용하지 않았다.  
그리고 Perm영역에 존재하면, GC의 대상이 되지 않았다.**  
**→ 그래서 Oracle 엔지니어들이 Java7에서 Perm 영역이 아닌 Heap 영역으로 Stirng Constant Pool의 위치를 변경시켜서**  
**GC의 대상이 되었다.**

* * *

#### **String은 두 가지 생성방식이 있고 각각의 차이점이 존재한다.**

1.  **new 연산자를 이용한 방식**
2.  **→ new를 통해 String을 생성하면 Heap 영역에 존재**
3.  **리터럴을 이용한 방식**
4.  **→ String Constant Pool이라는 영역에 존재**

#### **String C****onstant** **Pool (String Pool)**

**→ String 리터럴로 생성하면 해당 String 값은 Heap 영역 내 String Constant Pool에 저장되어 재사용된다.**  
**하지만, new 연산자로 생성하면 같은 내용이라도 여러 개의 객체가 각각 Heap 영역을 차지하게 된다.**

![](/assets/img/posts/257/1.png)

```java
public class StringPool {
    public static void main(String[] args) {
        String s1 = "Cat";
        String s2 = "Cat";
        String s3 = new String("Cat");
        String s4 = "Dog";

        // 객체 주소값 비교
        System.out.println(s1 == s2);
        System.out.println(s1 == s3);
        // 값 비교
        System.out.println(s1.equals(s3));
    }
}
```

```java
true
false
true
```

   
**Heap 영역에 생성된 String 객체와 리터럴을 이용해 String Constant Pool에 저장된 String 객체의 주소값은www**  
**다를 수밖에 없다.**  
   
**Heap 영역에는 "Cat", "Dog"와 같은 값들이 들어가게 되는데, 그림의 우측을 보면 중요한 차이를 발견할 수 있다.**

-   **String Literal로 생성한 객체는 String Pool에 들어간다**
-   **String Literal로 생성한 객체는 값이 이미 String Pool에 존재한다면, 해당 객체는 String Pool의**
-   **Reference를 참조한다**
-   **new 연산자로 생성한 String 객체는 값이 String Pool에 이미 존재하더라도 Heap 영역 내 별도의 객체를 가리킨다.**

* * *

#### **String interning**

**String을 Literal로 선언할 경우 내부적으로 String의 intern() 메서드가 호출하게 된다.**  
**intern() 메서드는 주어진 문자열이 String Constant Pool에 존재하는지 검색하여 존재한다면**   
**그 주소값을 반환하고 없다면 String Constant Pool에 넣고 새로운 주소값을 반환한다.**  
 

> **String Constant Pool에 저장하고 사용하는 것을 String Interning이라고 한다.**  
> **이렇게 String Interning에 저장된 String 값은 불변성(Immutability)을 가진다.**  
> **불변성을 가진다는 의미는 값은 변함이 없으며, 동일한 String 값을 가지고 있다면 같은 곳을 가리킨다는 의미다.**
