---
title: "[Design Pattern] 구조 패턴 - Bridge"
date: 2023-10-25 16:19:57 +0900
categories: ["Language", "Design Pattern"]
tags: ["bridge pattern", "디자인 패턴 bridge", "디자인 패턴 bridge 백기선", "디자인 패턴 브릿지", "디자인패턴 브릿지", "브릿지 패턴"]
tistory_url: "https://aroma-bok.tistory.com/entry/Design-Pattern-%EA%B5%AC%EC%A1%B0-%ED%8C%A8%ED%84%B4-Bridge"
---
### **브릿지 (Bridge) 패턴**

-   **추상적인 것과 구체적인 것을 분리하여 연결하는 패턴**
-   **브릿지 패턴의 중점은 '기능 클래스 계층'과 '구현 클래스 계층'의 분리**  
    ***기능 클래스 : 기본 기능을 가진 부모 클래스를 상속받아 새로운 기능이 추가된 하위 클래스 (상속)***  
    ***구현 클래스 : 기능을 정의한 추상 클래스 (또는 인터페이스)의 기능을 구현한 하위 클래스***
-   **기능과 구현 클래스 계층이 뭉쳐있으면 새로운 기능이 추가될수록 계층구조가 무거워지고**  
    **이로 인해, 1개의 클래스에는 권한이 너무 커지고 의존도가 높아지는 문제가 발생한다.**  
    **이러한 문제를 해결하기 위해 2가지 계층으로 분리해서 관리하고 연결하기 위한 패턴이 브릿지 패턴이다.**

* * *

**조금 이해가 잘 될 수 있도록 두 가지로 나눠서 생각해 보겠습니다.**

**1\. 새로운 기능을 추가하고 싶은 경우**

**→ '부모 클래스 A'를 상속받은 '자식 클래스 B'를 만든다. 이 과정에서 작은 클래스 계층이 생성되었다.**

**B 클래스는 기능을 추가하기 위해 만들어진 계층이다. 이렇게 되면, A 클래스는 기본적인 기능을 가지고 있고**  
**B클래스에는 새로운 기능을 추가할 수 있게 된다.** **이 클래스 계층을 '기능 클래스 계층'이라고 한다.**

**2\. 새로운 구현을 추가하고 싶은 경우**

**→ '상위클래스 A (추상 클래스 or 인터페이스)'를 상속 또는 구현하는 '하위 클래스 B' 클래스는 기능을 추가하기 위한 것도 아니고 새로운 메소드를 추가하기 위한 것도 아니다.** **단지, 상위 클래스의 기능을 구현하는 '구현 클래스 계층'이다.**

**즉, 우리는 하위 클래스를 만들려고 할 때, 의도를 확인해봐야 한다.**

***기능을 추가하려고 하는가?, 구현을 하려고 하는가?***

* * *

#### **브릿지 (Bridge) 패턴 적용하기**

-   **공통 기능을 정의해 둔 인터페이스**

```java
public interface Champion {
    // 공통 기능을 가진 챔피언 인터페이스
    void move();
    void skillQ();
    void skillW();
    void skillE();
    void skillR();
}
```

-   **Champion의 기능을 구현한 1개의 '구현 클래스 계층'**
-   **Skin을 외부에서 주입받아 멤버변수로 가지고 있다.**
-   **Skin은 인터페이스 이므로, Skin을 구현한 '구현 클래스'를 주입받아야 한다.**
-   **외부에서 주입받는 Skin 필드가 'DefaultChampion - 기능 클래스'와 'SkinA or SkinB - 구현 클래스'를 연결하는**  
    **Bridge 역할을 한다.**

```java
public class DefaultChampion implements Champion{
    private Skin skin;
    private String name;

    public DefaultChampion(Skin skin, String name) {
        this.skin = skin;
        this.name = name;
    }

    @Override
    public void move() {
        System.out.printf("%s %s move\n", skin.getName(), this.name);
    }

    @Override
    public void skillQ() {
        System.out.printf("%s %s Q skill\n", skin.getName(), this.name);
    }

    @Override
    public void skillW() {
        System.out.printf("%s %s W skill\n", skin.getName(), this.name);
    }

    @Override
    public void skillE() {
        System.out.printf("%s %s E skill\n", skin.getName(), this.name);
    }

    @Override
    public void skillR() {
        System.out.printf("%s %s R skill\n", skin.getName(), this.name);
    }
}
```

-   **어떤 스킨인지 알려주는 공통 기능 인터페이스**

```java
public interface Skin {
    // 스킨의 이름을 얻어오는 공통기능
    String getName();
}
```

-   **Skin을 구현한 각각의 Skin 구현체들**

```java
public class SkinA implements Skin{
    @Override
    public String getName() {
        return "A Skin";
    }
}
```

```java
public class SkinB implements Skin{
    @Override
    public String getName() {
        return "B Skin";
    }
}
```

-   **새로운 챔피언 생성**

```java
public class Ari extends DefaultChampion {
    public Ari(Skin skin) {
        super(skin, "Ari");
    }
}
```

```java
public class Akali extends DefaultChampion {
    public Akali(Skin skin) {
        super(skin, "Akali");
    }
}
```

```java
    @Test
    void test2() {
        Champion AskinAri = new Ari(new SkinA());
        AskinAri.move();   // A Skin Ari move
        AskinAri.skillQ(); // A Skin Ari Q skill

        Champion BskinAkali = new Akali(new SkinB());
        BskinAkali.move();  // B Skin Akali move
        BskinAkali.skillR();// B Skin Akali R skill
    }
```

* * *

#### **브릿지 (Bridge) 패턴을 적용하지 않는다면?**

-   **아래처럼 Skin별로 각 Champion을 구현하는 새로운 챔피언을 만들어야 한다.** 
-   **추가로 다른 특징들 (ex : 스킬)이 이 구조에 반영이 되면 걷잡을 수 없이 늘어나게 된다.**
-   **새로운 스킨이 나오면 새로운 스킨별로 캐릭터를 추가해야 한다. (상당히 번거롭다)**

```java
public class AskinAri implements Champion{
}

public class AskinAcali implements Champion{
}

public class BskinAri implements Champion{
}

public class BskinAcali implements Champion{
}
```

* * *

#### **이용되는 원리**

**1\. Abstraction 역할을 하는 DefaultChampion 클래스**

**→ 챔피언을 생성하고 싶다면 DefaultChampion을**

```java
public class DefaultChampion implements Champion{
    private Skin skin;
    private String name;

	// 생성자 및 재정의 기능 생략
}
```

```java
public class Ari extends DefaultChampion {
    public Ari(Skin skin) {
        super(skin, "Ari");
    }
}
```

**2\. Implementation 역할을 하는 Skin 클래스**

**→ Skin 인터페이스를 구현해서 새로운 Skin을 생성한다.**

```java
public class SkinA implements Skin{
    @Override
    public String getName() {
        return "A Skin";
    }
}
```

**3\. 브릿지(Bridge) 적용 전, 후 코드 비교**

**→ *패턴 적용 전 에는 직접 스킨별 챔피언 인스턴스를 생성해서 사용*했다.**

**하지만, *패턴을 적용한 후*에는, *Champion의 서브클래스에 Skin을 생성자로 주입받아서 호출하여 사용*하고 있다.**

```java
    @Test
    void test2() {
        // Bridge 패턴을 적용하지 않을 시 사용되는 코드
//        Champion AskinAri = new AskinAri();
//        AskinAri.skillQ();
//        AskinAri.skillR();

	// Bridge 패턴을 적용한 코드
        Champion AskinAri = new Ari(new SkinA());
        AskinAri.move();
        AskinAri.skillQ();
    }
```

#### ***DefaultChampion과 Skin의 계층구조에 영향을 주지 않고 현재 계층을 확장하고만 있습니다.***

* * *

**장점 : 브릿지(Bridge) 패턴을 사용함으로써, 각각의 계층에 영향을 주지 않고 독립적으로 확장하여 사용할 수 있다.  
**

**단점 : 계층 구조가 늘어나 복잡도가 증가할 수 있다.**
