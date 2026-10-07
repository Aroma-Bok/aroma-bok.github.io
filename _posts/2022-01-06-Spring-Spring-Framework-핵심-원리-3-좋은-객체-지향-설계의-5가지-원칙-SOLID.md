---
title: "[Spring] Spring Framework - 핵심 원리 (3) - 좋은 객체 지향 설계의 5가지 원칙 (SOLID)"
date: 2022-01-06 23:56:53 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["dip", "isp", "lsp", "ocp", "solid", "srp", "솔리드", "스프링"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-3-%EC%A2%8B%EC%9D%80-%EA%B0%9D%EC%B2%B4-%EC%A7%80%ED%96%A5-%EC%84%A4%EA%B3%84%EC%9D%98-5%EA%B0%80%EC%A7%80-%EC%9B%90%EC%B9%99-SOLID"
---
![](/assets/img/posts/60/1.png)

**좋은 객체란 뭘까? 많이 들어봤던 'SOLID'에 대해서 오늘은 정리를 해보려고 한다.**

**면접에서도 나오는 개념 중에 하나이므로 꼭 잘 이해해보자!** 

## **SOLID**

**1\. SRP - 단일 책임 원칙 (Single responsibility principle)**

**2\. OCP - 개방-폐쇄 원칙 (Open/closed principle)**

**3\. LSP - 리스코프 치환 원치식 (Liskov substitution principle)**

**4\. ISP - 인터페이스 분리 원칙 (Interface segregation principle)**

**5\. DIP - 의존관계 역전 원칙 (Dependency inversion principle)**

* * *

#### **SRP - 단일 책임 원칙 (Single responsibility principle)**

![](/assets/img/posts/60/2.png)

-   **한 클래스는 하나의 책임만 가져야 한다.** 
-   **하나의 책임이라는 것은 모호하다.**
-   **클 수 있고, 작을 수 있다.**
-   **문맥과 상황에 딸 다르다.**
-    **중요한 기준은 변경.  
    변경이 있을 때, 파급효과가 적으면 단일 책임 원칙을 잘 따른 것  
      
    예) UI 변경, 객체의 생성과 사용을 분리**

#### **OCP - 개방-폐쇄 원칙 (Open/closed principle)**

![](/assets/img/posts/60/3.png)

-   **소프트웨어 요소는 확장에는 열려 있으나 변경에는 닫혀 있어야 한다.**
-   **이런 거짓말 같은 말이?, 확장을 하려면, 당연히 기존 코드를 변경해야지?**
-   **다형성을 활용해보자.**
-   **인터페이스를 구현한 새로운 클래스를 하나 만들어서 새로운 기능을 구현**
-   **지금까지 배운 역할과 구현의 분리를 생각해보자**  
    **ex) 자동차가 바뀐다고 운전자가 운전을 못하는 것이 아니다.**

**문제점**

```java
// MemberRepository m = new MemoryMemberRepository(); // 구현체, 기존코드
MemberRepository m = new JdbcMemberRepository(); // 구현체, 변경코드
```

**\=> 기존의 코드를 주석 처리하고, 새로운 코드로 적용하려고 하고 있다. 하지만 결국은, 기존의 코드의 수정이 일어나고 있다.**

**구현 객체를 변경하려면 클라이언트 코드를 변경해야 한다. 분명 다형성을 사용했지만 'OCP 원칙'을 지킬 수 없다.**

**Q :이 문제를 어떻게 해결해야 하나?** 

**A: 객체를 생성하고, 연관관계를 맺어주는 별도의 조립, 설정자가 필요하다.**

**이 별도의 '설정자'가 '스프링의 컨테이너'가 해준다. 이 'OCP 원칙'을 지키기 위해서 'DI' & 'IOC'도 필요한 것이다.**

**말로 설명하기엔 너무나도 어려운 내용이다. 코드로 꼭 짜보면서 공부해보자!**

#### **LSP - 리스코프 치환 원치식 (Liskov substitution principle)**

![](/assets/img/posts/60/4.png)

-   **프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의  
    인스턴스로 바꿀 수 있어야 한다.**
-   **다형성에서 하위 클래스는 인터페이스 규약을 다 지켜야 한다는 것, 다형성을 지원하기 위한 원칙, 인터페이스를 구현한 구현체는 믿고 사용하려면, 이 원칙이 필요하다.**
-   **단순히 컴파일에 성공하는 것을 넘어서는 이야기  
    **  
    **예) 자동차 인터페이스의 엑셀은 앞으로 가라는 기능, 뒤로 가게 구현하면 LSP 위반, 느리더라도 앞으로 가야 함**

#### **ISP - 인터페이스 분리 원칙 (Interface segregation principle)**

![](/assets/img/posts/60/5.png)

-   **클라이언트는 자신이 사용하지 않는 메소드에는 의존하지 않아야 한다.  
    **
-   **특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다**
-   **자동차 인터페이스 -> 운전 인터페이스, 정비 인터페이스로 분리**
-   **사용자 클라이언트 -> 운전자 클라이언트, 정비사 클라이언트로 분리**
-   **분리하면 정비 인터페이스 자체가 변해도 운전자 클라이언트에 영향을 주지 않음**
-   **인터페이스가 명확해지고, 대체 가능성이 높아진다**

#### **DIP - 의존관계 역전 원칙 (Dependency inversion principle)**

![](/assets/img/posts/60/6.png)

-   **프로그래머는 '추상화에 의존해야지, 구체화에 의존하면 안 된다.'  
    의존성 주입은 이 원칙을 따르는 방법 중 하나이다.**
-   **쉽게 이야기해서, 구현 클래스에 의존하지 말고, 인터페이스에 의존하라는 뜻이다.**
-   **앞에서 이야기한 역할(Role)에 의존하게 해야 한다는 것과 같다. 객체 세상도 클라이언트가 인터페이스에 의존해야 유연하게 구현체를 변경할 수 있다.  
    구현체에 의존하게 되면 변경이 아주 어려워진다.**

**그런데 'OCP'에서 설명한 'MemberService'는 인터페이스에 의존하지만, 구현 클래스도 동시에 의존한다.**

**'MemberService' 클라이언트가 구현 클래스를 직접 선택**

```java
MemberService m = new MemoryMemberRepository();
```

**DIP 위반 = 인터페이스와 구현 클래스 둘 모든 것에 의존하고 있다는 것.**

**아래 코드를 보자.**

**1번째 코드는 'MemberRepository- 인터페이스' 필드를 가지고 있다. 하지만 오른쪽에는 'MemoryMemberRepository-구현체'를 할당하고 있다. 그러면, 'MemberService'는 'MemoryMemberRepository'도 의존하고 있다는 것이다.**

**즉, 'MemberService'는 'MemberRepository- 인터페이스'만 알고 있는 게 아니라,**

**'MemoryMemberRepository-구현체'까지 알고 있는 것이다.**

**그래서 'MemoryMemberRepository'를 다룬 걸로 바꾸려고 할 때, 코드의 변경이 일어나는 것이다.**

```java
public class MemberService{
	MemberRepository m = new MemoryMemberRepository();
}

public class MemberService{
	//MemberRepository m = new MemoryMemberRepository();
	MemberRepository m = new JdbcMemberRepository();
}
```

* * *

#### **정리** 

-   **객체 지향의 핵심은 다형성**
-   **다형성 만으로는 쉽게 부품을 갈아 끼우듯이 개발할 수 없다.**
-   **다형성 만으로는 구현 객체를 변경할 때 클라이언트 코드도 함께 변경된다.**
-   **다형성 만으로는 'OCP' & 'DIP'를 지킬 수 없다.**
-   **그래서 뭔가 더 필요하다.**

## **그 뭔가가 바로 '스프링'이다.**
