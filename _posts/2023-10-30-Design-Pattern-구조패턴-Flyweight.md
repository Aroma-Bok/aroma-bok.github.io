---
title: "[Design Pattern] 구조패턴 - Flyweight"
date: 2023-10-30 12:16:38 +0900
categories: ["Language", "Design Pattern"]
tags: ["디자인패턴 flyweight", "디자인패턴 flyweight 백기선", "디자인패턴 플라이웨이트 백기선", "디자인패턴 플라이웨이트 패턴"]
tistory_url: "https://aroma-bok.tistory.com/entry/Design-Pattern-%EA%B5%AC%EC%A1%B0%ED%8C%A8%ED%84%B4-Flyweight"
---
### **플라이웨이트 (Flyweight) 패턴**

-   **객체를 가볍게 만들어 메모리 사용을 줄이는 목적을 가진 패턴**
-   **자주 변하는 속성과 변하지 않는 속성을 분리하고 재사용하여 메모리 사용을 줄이는 구조를 설계하는 패턴**
-   **주로 비교적 많은 인스턴스를 가지고 있는 애플리케이션에 사용되는 패턴**
-   **인스턴스가 많으면 메모리를 잡아먹고 OOM (Out Of Memory) 문제가 발생할 수 있기에 이를 예방하기 위함**
-   **공통으로 사용하는 클래스(Flyweight)를 생성하는 팩토리 클래스(Flyweight Factory)를 만들어,**
-   **인스턴스를 최초 1개만 생성하고 공유하여 재사용**

#### **구조**

-   **Flyweight : 공유하여 사용하는 클래스 또는 API**
-   **Flyweight Factory : 인스턴스를 생성 또는 공유 (공장 역할)**
-   **Client : Flyweight 인스턴스를 필요로 하는 클라이언트**

![](/assets/img/posts/241/1.png)

#### **사용시기**

-   **공통적인 인스턴스를 많이 생성하는 로직이 포함된 경우**
-   **자주 변하지 않는 속성을 재사용할 수 있는 경우**

* * *

#### **요구사항**

-   **편집기에 글을 쓸 수 있어야 한다.**
-   **편집기니까 한 글자마다 폰트와 글씨 색, 크기를 바꿀 수 있어야 한다.**

* * *

#### **플라이웨이트 (Flyweight) 패턴 적용 전**

-   **글자 하나를 표현한 도메인**

```java
public class Character {
    private char value;
    private String color;
    private String fontFamily;
    private int fontSize;

    public Character(char value, String color, String fontFamily, int fontSize) {
        this.value = value;
        this.color = color;
        this.fontFamily = fontFamily;
        this.fontSize = fontSize;
    }
}
```

-   **편집기에 글을 입력하는 클라이언트**
-   **글자 하나마다 새로운 Character 인스턴스 생성**

```java
    @Test
    void client1() {
        Character c1 = new Character('H',"yellow", "Nanum", 12);
        Character c2 = new Character('I',"white", "Nanum", 12);
    }
```

   
**글자 하나마다 새로운 생성자를 통해 객체를 생성하게 되는데 이렇게 메모리를 잡아먹는 것을 잡아야한다.**  
**그래서 메모리를 아끼기 위해 자주 변하는 속성과 자주 변하지 않는 속성을 분류해야 한다.**

* * *

#### **플라이웨이트 (Flyweight) 패턴 적용 후**

-   **문자와 색 : 자주 변하는 속성**
-   **글자 폰트와 크기 : 자주 변하지 않는 속성**

-   **Character 인스턴스가 가지는 속성 중에서 자주 변하는 속성과 자주 변하지 않는 속성을 분리**
-   **자주 변하지 않는 '폰트와 크기'를 묶어서 클래스 선언**
-   **공유객체 이므로 final로 불변으로 선언**

```java
public class Font {
    final String family;

    final int size;

    public Font(String family, int size) {
        this.family = family;
        this.size = size;
    }

    public String getFamily() {
        return family;
    }

    public int getSize() {
        return size;
    }
}
```

-   **공유받는 Font 클래스를 필드로 받는다.**

```java
public class Character {
    private char value;
    private String color;

    private Font font;
    public Character(char value, String color, Font font) {
        this.value = value;
        this.color = color;
        this.font = font;
    }
}
```

-   **Font 생성을 관리해 주는 팩토리 생성**
-   **getFont()를 통해서 Font 생성을 하게 되면, Cache에 데이터가 있는지 먼저 검사**
-   **Cache에 존재하면 새로 생성하지 않고 기존 인스턴스 반환**

```java
public class FontFactory {
    private Map<String, Font> cache = new HashMap<>();
    
    public Font getFont(String font) {
        if(cache.containsKey(font)) {
            return cache.get(font);
        }else {
            String[] split = font.split(",");
            
            //폰트이름, 폰트사이즈 분리
            Font newFont = new Font(split[0], Integer.parseInt(split[1]));
            cache.put(font, newFont);
            return newFont;
        }
    }
}
```

```java
    @Test
    void client1() {
        // 플라이웨이트 패턴 적용 후 테스트 코드
        FontFactory fontFactory = new FontFactory();
        Character c1 = new Character('H',"white",fontFactory.getFont("Nan-um,12"));
        Character c2 = new Character('I',"white",fontFactory.getFont("Nan-um,12"));

        System.out.println(c1.getFont()); // Flyweight.Font@41d477ed
        System.out.println(c2.getFont()); // Flyweight.Font@41d477ed
        // 같은 인스턴스 사용
    }
```

> **Flyweight로 추출한 객체는 불변해야 하기 때문에 final로 선언되어야 한다.**  
> **불변 객체가 아니라면, 데이터 변경이 일어날 수 있기 때문에 final로 선언해 변경이 불가하도록 만들어야 한다.**

* * *

#### **싱글톤 (Singleton)과의 차이점?**

-   **싱글톤 패턴은 '클래스 자체'가 오직 1개의 인스턴스만을 가지도록 제한하지만.**  
    **플라이웨이트 패턴은 '팩토리'가 제어하는 것이다.**
-   **즉, *인스턴스 생성의 제한을 어디서 하느냐에 차이***

* * *

#### **플라이웨이트 (Flyweight) 패턴**

**장점**

-   **많은 객체를 만들 때 성능을 향상할 수 있다.**
-   **많은 객체를 만들 때 메모리를 줄일 수 있다.**

**단점**

-   **특정 인스턴스를 다르게 처리하는 게 힘들다.**
