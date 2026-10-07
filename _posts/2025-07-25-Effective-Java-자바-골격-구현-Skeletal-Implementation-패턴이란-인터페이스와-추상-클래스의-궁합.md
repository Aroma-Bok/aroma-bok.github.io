---
title: "[Effective Java] 자바 골격 구현 (Skeletal Implementation) 패턴이란? - 인터페이스와 추상 클래스의 궁합"
date: 2025-07-25 16:48:33 +0900
categories: ["Language", "Effective Java"]
tags: ["골격 구현 패턴", "이펙티브자바 골격구현", "자바 골격구현패턴"]
tistory_url: "https://aroma-bok.tistory.com/entry/Effective-Java-%EC%9E%90%EB%B0%94-%EA%B3%A8%EA%B2%A9-%EA%B5%AC%ED%98%84-Skeletal-Implementation-%ED%8C%A8%ED%84%B4%EC%9D%B4%EB%9E%80-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4%EC%99%80-%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4%EC%9D%98-%EA%B6%81%ED%95%A9"
---
**자바에서는 인터페이스는 유연하지만 구현이 없고, 추상 클래스는 구현은 가능하지만 다중 상속이 불가능하다는 특성이 있다.** **이런 단점을 보완하기 위해 <이펙티브 자바>에서는 다음과 같이 조언한다.**

> **가능하면 인터페이스를 사용하되, 공통 구현이 필요하다면 추상 클래스와 함께 사용하라**

**이 방식이 바로 골격 구현(Skeletal Implementation) 패턴이다.**

* * *

### **골격 구현이란?**

-   **인터페이스 + 추상 클래스 조합**  
    **공통 로직은 추상 클래스에, 핵심 구현만 서브 클래스에서 직접 작성하는 구조**

### **장점**

-   **인터페이스의 유연성 유지**
-   **공통 구현 코드 재사용**
-   **새로운 구현 클래스 추가 시 편리함**

### **예제**

#### **1\. 인터페이스 정의**

```java
public interface GameCharacter {
    void move();
    void attack();
    void defend();
}
```

#### **2\. 골격 구현 추상 클래스**

```java
public abstract class AbstractGameCharacter implements GameCharacter {
    @Override
    public void move() {
        System.out.println("기본 이동: 앞으로 1칸 전진");
    }

    @Override
    public void defend() {
        System.out.println("기본 방어: 방패로 막기");
    }

    @Override
    public abstract void attack();
}
```

#### **3\. 실제 구현 클래스**

```java
public class Warrior extends AbstractGameCharacter {
    @Override
    public void attack() {
        System.out.println("전사 공격: 검으로 베기");
    }
}

public class Archer extends AbstractGameCharacter {
    @Override
    public void attack() {
        System.out.println("궁수 공격: 활로 원거리 사격");
    }
}
```

* * *

### **골격 구현을 사용하지 않는 경우는?**

**→ 모든 구현 클래스를 인터페이스만으로 구현하게 되면, 공통 코드도 매번 반복해서 사용해야 한다.**

### **예시**

#### **1\. 인터페이스 정의**

```java
public interface GameCharacter {
    void move();
    void attack();
    void defend();
}
```

#### **2\. 구현 클래스 - Warrior**

```java
public class Warrior implements GameCharacter {
    @Override
    public void move() {
        System.out.println("기본 이동: 앞으로 1칸 전진");
    }

    @Override
    public void attack() {
        System.out.println("전사 공격: 검으로 베기");
    }

    @Override
    public void defend() {
        System.out.println("기본 방어: 방패로 막기");
    }
}
```

#### **3\. 구현 클래스 - Archer**

```java
public class Archer implements GameCharacter {
    @Override
    public void move() {
        System.out.println("기본 이동: 앞으로 1칸 전진");
    }

    @Override
    public void attack() {
        System.out.println("궁수 공격: 활로 원거리 사격");
    }

    @Override
    public void defend() {
        System.out.println("기본 방어: 방패로 막기");
    }
}
```

### **단점**

-   **move(), defend()와 같은 공통 기능이 클래스마다 중복**
-   **공통 로직 수정 시, 모든 클래스를 일일이 고쳐야 함**
-   **유지보수 어려움**

* * *

### **골격 구현 패턴의 장단점**

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft"><tbody><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>구분</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>골격 구현 사용</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>인터페이스만 사용</b></span></td></tr><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>코드 중복</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>최소화</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>심함</b></span></td></tr><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>구현 편의성</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>높음</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>낮음</b></span></td></tr><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>유연성</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>추상 클래스 상속 1개 제한</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>높음</b></span></td></tr><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>공통 로직 관리</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>중앙 집중화</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>분산되어 있음</b></span></td></tr></tbody></table>

### **결론**

-   **인터페이스는 계약(약속 | 규칙), 추상 클래스는 공통 구현의 역할**
-   **복잡한 구조나 중복 제거가 필요한 경우, 골격 구현을 활용하면 생산성과 유지보수성이 좋아짐**
-   **자바 표준 라이브러리에서도 AbstractList, AbstractSet, AbstractMap 등에서 적극 활용 중**
