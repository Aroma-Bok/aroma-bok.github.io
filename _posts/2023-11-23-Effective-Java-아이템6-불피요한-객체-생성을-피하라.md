---
title: "[Effective Java] 아이템6 - 불피요한 객체 생성을 피하라"
date: 2023-11-23 15:20:01 +0900
categories: ["Language", "Effective Java"]
tags: ["effective java", "effective java item6", "effective java 아이템6", "이펙티브 자바 아이템6", "이펙티브자바 item6", "이펙티브자바 아이템6"]
tistory_url: "https://aroma-bok.tistory.com/entry/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C6-%EB%B6%88%ED%94%BC%EC%9A%94%ED%95%9C-%EA%B0%9D%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%9D%84-%ED%94%BC%ED%95%98%EB%9D%BC"
---
**객체들을 사용하다 보면 불필요하게 여러 번 생성하는 경우가 있다.**

**같은 기능의 객체를 새로 생성하는 대신, 객체 하나를 재사용하는 편이 좋을 때가 많다.**

**특히, 불변 객체는 언제든 재사용할 수 있다.**

**예를 들어 세 가지를 살펴보자.**

* * *

#### **1\. 문자열 객체 생성**

**String을 new로 생성하면 항상 새로운 객체를 만들게 된다.**

```java
String s = new String("Java");
```

**아래 코드는 새로운 인스턴스를 매번 만드는 대신 하나의 Stirng 인스턴스를 재사용한다.**

```java
String a = "Java";
```

**같은 가상 머신 안에서 이와 똑같은 문자열 리터럴을 사용하는 모든 코드가 같은 객체를 재사용함이 보장된다.**

**※ String Constant Pool에 존재하면 기존 객체를 참조하게 된다.**

**(내용 참조 [https://aroma-bok.tistory.com/entry/Java-String-Constant-Pool-String-Pool-%EA%B0%9C%EB%85%90](https://aroma-bok.tistory.com/entry/Java-String-Constant-Pool-String-Pool-%EA%B0%9C%EB%85%90 "여기"))**

* * *

#### **2\. 정규식 생성**

**정규표현식은 한번 만들 때 CPU 리소스를 많이 사용하기 때문에 매번 인스턴스를 생성하기는 부담스럽다.**

#### **비효율적인 정규식 활용**

**문자열을 matches() 메서드를 사용해서 정규식에 포함되는지 확인할 때 matches() 메서드 인자로 정규식을 받는데**

**내부적으로 컴파일을 하게 된다. 하지만 이 패턴을 만들어서 컴파일하는 과정이 오래 걸리는데**

**isRomanNumeralSlow() 메서드를 반복적으로 호출하는 것은 굉장히 비효율적이다.**

```java
public class Main {
    public static void main(String[] args) {
        boolean result = false;

        long start = System.nanoTime();

        for(int j=0; j<100; j++) {
            result = RomanNumerals.isRomanNumeralSlow("MCMLXXVI");
        }
        long end = System.nanoTime();
        System.out.println(end - start);
        System.out.println(result);
    }
}
```

```java
public class RomanNumerals {
    static boolean isRomanNumeralSlow(String s) {
        return s.matches("^(?=.)M*(C[MD]|D?C{0,3})(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
    }
}
```

```java
17115300
true
```

#### **효율적인 정규식 활용**

**동일한 패턴이 여러 번 사용이 된다면 필드를 선언해서 사용하는 것을 책에서 권장한다.**

```java
public class RomanNumerals2 {
    private static final Pattern ROMAN = Pattern.compile(
            "^(?=.)M*(C[MD]|D?C{0,3})(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
            
    static boolean isRomanNumeralFast(String s) {
        return ROMAN.matcher(s).matches();
    }
}
```

**실행시간이 많이 줄어든 것을 확인할 수 있다.**

```java
4304500
true
```

* * *

#### **3\. 오토박싱과 언박싱**

#### **불필요한 오토박싱이 일어나는 경우**

**JVM에서 자동으로 long인 primitive 타입을 Long인 sum에 더하기 위해서 자동으로 오토박싱을 해서** 

**Wrapper 타입인 Long으로 바꿔서 계산한다.**

```java
public class Sum {
    public static long sum() {
        Long sum = 0L;

        for(long i=0; i<Integer.MAX_VALUE; i++) {
            sum += 1; // long -> Long 타입으로 오토박싱 발생
        }
        return sum;
    }
}
```

**실행시간 측정**

```java
    @Test
    void test2() {
        long start = System.nanoTime();
        long x = Sum.sum();
        long end = System.nanoTime();
        System.out.println((end - start) / 1_000_000. + " ms.");
        System.out.println(x);
    }
```

```java
6277.089 ms.
2147483647
```

#### **오토박싱이 일어나지 않는 경우**

```java
public class Sum2 {
    public static long sum() {
        long sum = 0L;

        for(long i=0; i<Integer.MAX_VALUE; i++) {
            sum += 1; // 둘 다 long 타입으로 오토박싱 발생 하지않음
        }
        return sum;
    }
}
```

```java
    @Test
    void test3() {
        long start = System.nanoTime();
        long x = Sum2.sum();
        long end = System.nanoTime();
        System.out.println((end - start) / 1_000_000. + " ms.");
        System.out.println(x);
    }
```

**실행시간이 6277 → 652로 엄청 많이 줄어든 것을 확인할 수 있다.**

```java
652.7016 ms.
2147483647
```
