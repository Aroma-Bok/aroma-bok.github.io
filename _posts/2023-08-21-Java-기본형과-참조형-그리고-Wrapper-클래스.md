---
title: "[Java] 기본형과 참조형 그리고 Wrapper 클래스"
date: 2023-08-21 16:19:15 +0900
categories: ["Language", "Java"]
tags: ["java 기본형 참조형 차이", "wrapper 클래스", "wrapper 클래스 사용이유"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-%EA%B8%B0%EB%B3%B8%ED%98%95%EA%B3%BC-%EC%B0%B8%EC%A1%B0%ED%98%95-%EA%B7%B8%EB%A6%AC%EA%B3%A0-Wrapper-%ED%81%B4%EB%9E%98%EC%8A%A4"
---
**Java에서는 데이터 유형을 기본형(Primitive)과 참조형(Reference)으로 나눌 수 있다.**

**변수의 성격과 동작방식에 차이가 있는데 그 부분을 알아보자.**

* * *

#### **기본형**

**데이터 값을 직접 저장하는 데이터 유형으로, Java의 기본 데이터 타입이다.**

**기본형은 스택(Stack) 메모리에 직접 값을** **저장하므로 메모리 사용량이 상대적으로 적다.**

**종류**

<table style="border-collapse: collapse; width: 64.3023%; height: 156px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 20px;"><td style="width: 50%; text-align: center; height: 20px;"><b><span style="font-family: 'Noto Serif KR';">종류</span></b></td><td style="width: 50%; text-align: center; height: 20px;"><b><span style="font-family: 'Noto Serif KR';">크기 (1byte = 8bit)</span></b></td></tr><tr style="height: 17px;"><td style="width: 50%; height: 17px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">boolean</span></b></td><td style="width: 50%; height: 17px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">1byte</span></b></td></tr><tr style="height: 17px;"><td style="width: 50%; height: 17px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">char</span></b></td><td style="width: 50%; height: 17px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">2byte</span></b></td></tr><tr style="height: 17px;"><td style="width: 50%; height: 17px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">byte</span></b></td><td style="width: 50%; height: 17px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">1byte</span></b></td></tr><tr style="height: 17px;"><td style="width: 50%; height: 17px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">short</span></b></td><td style="width: 50%; height: 17px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">2byte</span></b></td></tr><tr style="height: 17px;"><td style="width: 50%; height: 17px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">int</span></b></td><td style="width: 50%; height: 17px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">4byte</span></b></td></tr><tr style="height: 17px;"><td style="width: 50%; height: 17px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">long</span></b></td><td style="width: 50%; height: 17px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">8byte</span></b></td></tr><tr style="height: 17px;"><td style="width: 50%; height: 17px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">float</span></b></td><td style="width: 50%; text-align: center; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">4byte</span></b></td></tr><tr style="height: 17px;"><td style="width: 50%; height: 17px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">double</span></b></td><td style="width: 50%; text-align: center; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">8byte</span></b></td></tr></tbody></table>

* * *

#### **참조형**

**객체(Object)를 가리키는 주소(Reference)를 저장하는 데이터 유형. 실제 데이터는 힙(Heap) 메모리에 저장되며,**

**스택(Stack) 메모리에는 객체의 주소가 저장된다. Java의 클래스, 인터페이스, 배열등은 모두 참조형이다.**

**예를 들어, String, ArrayList, Object들이 참조형 데이터 타입이다.**

* * *

#### **차이점**

**값 저장 방식**

-   **기본형 : 실제 값이 스택(Stack)에 저장**
-   **참조형 : 객체의 주소가 스택(Stack)에 저장되며, 실제 데이터는 힙(Heap)에 저장**

**메모리 사용량**

-   **기본형 : 메모리 사용량이 상대적으로 적다**
-   **참조형 : 객체의 크기와 상관없이 주소를 저장하므로 메모리 사용량이 더 많을 수 있다**

**값의 변경**

-   **기본형 : 변수에 저장된 값이 직접 변경**
-   **참조형 : 변수에 저장된 주소를 통해 객체 내부의 값이 변경**

**초기화**

-   **기본형 : 초기화하지 않으면 기본값 (0 또는 false)으로 초기화된다**
-   **참조형 : 초기화하지 않으면 null로 초기화된다**

* * *

#### **Wrapper 클래스**

**Java의 기본 데이터 타입을 객체로 감싸는 클래스**

**기본 데이터 타입을 객체로 다루기 위해 대응하는 Wrapper 클래스가 존재한다.**

<table style="border-collapse: collapse; width: 68.3721%; height: 156px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 20px;"><td style="width: 50%; text-align: center; height: 20px;"><b><span style="font-family: 'Noto Serif KR';">종류</span></b></td><td style="width: 50%; text-align: center; height: 20px;"><b><span style="font-family: 'Noto Serif KR';">기본타입(Primitive)</span></b></td></tr><tr style="height: 17px;"><td style="width: 50%; text-align: center; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">Byte</span></b></td><td style="width: 50%; text-align: center; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">byte</span></b></td></tr><tr style="height: 17px;"><td style="width: 50%; text-align: center; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">Short</span></b></td><td style="width: 50%; text-align: center; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">short</span></b></td></tr><tr style="height: 17px;"><td style="width: 50%; text-align: center; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">Integer</span></b></td><td style="width: 50%; text-align: center; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">int</span></b></td></tr><tr style="height: 17px;"><td style="width: 50%; text-align: center; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">Long</span></b></td><td style="width: 50%; text-align: center; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">long</span></b></td></tr><tr style="height: 17px;"><td style="width: 50%; text-align: center; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">Float</span></b></td><td style="width: 50%; text-align: center; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">float</span></b></td></tr><tr style="height: 17px;"><td style="width: 50%; text-align: center; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">Double</span></b></td><td style="width: 50%; text-align: center; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">double</span></b></td></tr><tr style="height: 17px;"><td style="width: 50%; text-align: center; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">Character</span></b></td><td style="width: 50%; text-align: center; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">char</span></b></td></tr><tr style="height: 17px;"><td style="width: 50%; text-align: center; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">Boolean</span></b></td><td style="width: 50%; text-align: center; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">boolean</span></b></td></tr></tbody></table>

**사용하는 이유**

-   **객체 지향 프로그래밍과 호환성**  
    **: Java는 객체 지향 언어이기 때문에 클래스와 객체를 기반으로 프로그래밍하는 것이 일반적이다.  
    하지만 기본 데이터 타입은 원시적인 값만 저장하고 동작하기 때문에 객체 지향 프로그래밍과 어울리지 않을 수 있다.**  
    **그래서 Wrapper 클래스는 기본 데이터 타입을 객체로 래핑 하여 객체 지향 프로그래밍과 호환성을 높여준다.**
-   **컬렉션과 제네릭 사용**  
    **: 컬렉션 프레임워크와 제네릭은 객체를 다루는 중요한 개념이다. 하지만 기본 데이터 타입은 객체가 아닌 값이므로,  
    컬렉션에 직접 저장하거나 제네릭으로 사용하는 것이 어려울 수 있다. Wrapper 클래스를 사용하면 기본 데이터 타입도 컬렉션에 저장하고 제네릭으로 사용할 수 있다.**
-   **Null 값 처리**  
    **: 기본 데이터 타입은 null을 가질 수 없다. 하지만 Wrapper 클래스는 null을 허용하므로, 예외적인 상황에서 사용된다.**
-   **메서드 활용**  
    **: Wrapper 클래스는 기본 데이터 타입에 대한 다양한 메서드와 기능을 제공한다. 예를 들어, Integer 클래스는 문자열을 정수로 변환하는 parseInt() 메서드와 같은 유용한 메서드를 제공한다.**

**즉, Wrapper 클래스는 기본 데이터 타입과 객체 지향 프로그래밍 간의 간극을 줄이고, 더 다양한 기능과 유연성을 제공하여 Java 프로그래밍을 더 효율적으로 만들어준다.**
