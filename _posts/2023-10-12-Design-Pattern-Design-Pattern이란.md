---
title: "[Design Pattern] Design Pattern이란?"
date: 2023-10-12 12:20:12 +0900
categories: ["Language", "Design Pattern"]
tags: ["design pattern", "design pattern 이란", "gof"]
tistory_url: "https://aroma-bok.tistory.com/entry/Design-Pattern-Design-Pattern%EC%9D%B4%EB%9E%80"
---
#### **Design Pattern**

-   **소프트웨어 설계 방법**
-   **반복되는 패턴처럼 자주 나타나는 클래스 간의 관계를 맺는 방법**

**소프트웨어 개발은 '****요구사항 분석 > 설계 > 구현 > 테스트' 과정을 반복하는데  
****설계를 올바르고 효과적으로 하기 위해 사용한다.**

**클래스 관계를 올바르게 맺어야 하는 이유**

1.  **클래스는 최소한의 단위 기능을 가짐. (즉, 하나의 기능만 갖는 게 좋음)**
2.  **큰 기능은 이러한 단위 기능을 갖는 클래스들 간의 관계를 통해 개발됨**
3.  **꼭 필요한 것들만으로 구성된 최적화된 소프트웨어 개발이 용이함**
4.  **문제 발생시 최소한의 코드 수정으로 유지보수가 용이함**
5.  **기존 기능에 영향을 주지 않고 새로운 기능 추가가 용이함**

**GoF (Gang of Four) 디자인 패턴**

-   **에리히 감마(Erich Gamma), 리처드 헬름(Richard Helm), 랄프 존슨(Ralph Johnson), 존 블리시데스(John Vlissides)**
-   ****총 23개의 패턴으로 구성되며 생성 패턴(5개), 구조 패턴(7개), 행위 패턴(11개)으로 분류****

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft"><tbody><tr><td style="width: 33.3333%;">생성패턴</td><td style="width: 33.3333%;">구조패턴</td><td style="width: 33.3333%;">행동패턴</td></tr><tr><td style="width: 33.3333%;">Abstract Factory<br>Builder<br>Factory Method<br>ProtoType<br>Singleton</td><td style="width: 33.3333%;">Bridge<br>Decorator<br>Facade<br>Flyweight<br>Proxy<br>Composite<br>Adapter</td><td style="width: 33.3333%;">Mediator<br>Interpreter<br>Iterator<br>Command<br>Chain of Responsibility<br>Memento<br>Observer<br>State<br>Strategy<br>Template Method<br>Visitor</td></tr></tbody></table>

* * *

**디자인 패턴은 외울필요가 없고, 설계에 그대로 적용하지 않아도 된다.**

**클래스들 간의 최적의 관계를 효과적으로 설계할 수 있는 하나의 사례로 받아들이고 이해하자.**
