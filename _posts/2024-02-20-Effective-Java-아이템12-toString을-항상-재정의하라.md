---
title: "[Effective Java] 아이템12 -  toString을 항상 재정의하라"
date: 2024-02-20 14:47:36 +0900
categories: ["Language", "Effective Java"]
tags: ["effective java", "effective java item12", "effective java tostring", "effective java 아이템 12", "이펙티브 자바 아이템 12"]
tistory_url: "https://aroma-bok.tistory.com/entry/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C12-toString%EC%9D%84-%ED%95%AD%EC%83%81-%EC%9E%AC%EC%A0%95%EC%9D%98%ED%95%98%EB%9D%BC"
---
**toString()은 Object 클래스의 public 메서드이다.**  
**기본적으로 toString()은 "클래스의 이름@hashCode(16진수 변환)" 값을 반환한다.**

**기본적으로 이 값은 사람이 읽기 어려울 뿐 아니라 쓸모없는 메시지로 남게 된다.**  
  
**가급적이면 그 객체가 가진 주요 정보 모두를 반환하는 게 좋다.**

**객체가 크거나 문자열로 표현하기에 적합하지 않다면 요약정보로 표현해도 된다.**

* * *

#### **toString을 구현할 때 반환값의 포맷 문서화에 따른 장단점**

**장점 \- 포맷을 명시하면, 그 객체는 표준적이고, 명확하고, 사람이 읽을 수 있게 된다.**  
**단점 \- 포맷을 명시하면, 평생 그 포맷에 얽매이게 된다. 프로그래머들이 그 포맷에 맞춰 파싱이 필요하다.**  
  
**반대로 포맷을 명시하지 않는다면,**

**향후 릴리스에서 정보를 더 넣거나 포맷을 개선할 수 있는 유연성을 얻게 된다.**  
  
***포맷을 명시하든 아니든 의도는 분명히 알 수 있도록 주석은 달아두자*.**

```java
/**
 * 이 전화번호의 문자열 표현을 반환한다.
 * 이 문자열은 "XXX-YYY-ZZZZ" 형태의 12글자로 구성된다.
 * XXX는 지역코드, YYY는 프리픽스, ZZZZ는 가입자번호이다.
 *
 * 전화번호의 각 부분의 값이 너 작아서 자릿수를 채울 수 없다면,
 * 앞에서부터 0으로 채워나간다. 예를들어 가입자 번호가 123이라면, 전화번호의 마지막 네 문자는 "0123"이다.
 * @return
 */
@Override
public String toString() {
    return String.format("%03d-%03d-%04d", areaCode, prefix,lineNum);
}
```

* * *

  
**toString 메서드는 아래 상황에서 자동으로 호출된다.**

1.  **printf, println**
2.  **문자열 연결 연산자 (+)**
3.  **assert 구문에 넘길 때**
4.  **디버거가 객체를 출력할 때**

* * *

#### **toString규약** 

**1. 사람이 읽기 쉬운 정보를 반환하라.**  
**2. 모든 하위 클래스에서 재정의하라.**

* * *

#### **핵심정리**

1.  **Object의 toString은 모든 구체 클래스에서 재정의하면 좋다.**
2.  **객체의 정보를 읽기 쉽고 명확하게 작성하자.**
3.  **디버깅과 코딩이 편리해진다.**
