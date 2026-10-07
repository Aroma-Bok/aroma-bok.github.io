---
title: "[Effective Java] 아이템10 - equals는 일반 규악을 지켜 재정의하라"
date: 2024-02-19 22:59:56 +0900
categories: ["Language", "Effective Java"]
tags: ["effective java", "effective java item 10", "이펙티브 자바", "이펙티브 자바 item 10", "이펙티브 자바 item10", "이펙티브자바"]
tistory_url: "https://aroma-bok.tistory.com/entry/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C10-equals%EB%8A%94-%EC%9D%BC%EB%B0%98-%EA%B7%9C%EC%95%85%EC%9D%84-%EC%A7%80%EC%BC%9C-%EC%9E%AC%EC%A0%95%EC%9D%98%ED%95%98%EB%9D%BC"
---
**결론부터 말하면 이렇다.**

  
**꼭 필요한 경우가 아니라면, equals를 재정의하지 말자.**

**재정의해야 하는 경우라면 클래스의 핵심 필드 모두가 5가지 규약을 지켜가며 비교하자.**

* * *

  
**equals 메서드는 재정의하기 쉬워 보이지만, 곳곳에 함정이 도사리고 있어서 자칫하면 끔찍한 결과를** 

**초래할 수 있다.**  
**문제를 회피하기 가장 좋은 방법은 재정의를 하지 않는 것이다.** 

**그냥 두면 그 클래스의 인스턴스는 오직 자기 자신과만 같게 된다.**  
**그러니 다음에서 열거한 상황 중 하나에 해당한다면 재정의하지 않는 것이 최선이다.**

* * *

#### **1\. 각 인스턴스가 본질적으로 고유하다.**

**값을 표현하는 게 아니라 동작하는 개체를 표현하는 클래스가 여기에 해당한다. Thread가 좋은 예로 Object의 equals 메서드는 이러한 클래스에 딱 맞게 구현되었다.**  
**Thread는 값이 아닌 동작 개체를 표현하는 클래스이기 때문에 동일한 인스턴스가 애초에 없다. Object의 equals로 충분하다.**

#### **2. 인스턴스의 논리적 동치성을 검사할 일이 없는 경우**

**값을 비교해서 동등한 지 비교할 일이 없다면 논리적 동치성 검사를 할 일이 없다는 것이다.**  
**java.util.regex.Pattern은 equals를 재정의해서 두 Pattern의 인스턴스가 같은 정규표현식을 나타내는지를 검사하는 방법이다.**  
**하지만, 클라이언트가 필요하다고 판단하지 않을 수 있기 때문에 재정의하지 않고 Object의 기본 equals만으로 해결된다.**

#### **3. 상위 클래스에서 재정의한 equals가 하위 클래스에도 딱 들어맞는 경우**

**상위 클래스에서 구현한 equals 메서드로 충분한 경우 재정의할 것이 아니라 상위 클래스에 정의된**

**equals를 사용하면 된다.**  
**예를 들어, Set 구현체는 AbstractSet이 구현한 equals를 상속받아 쓰고, List는 AbstractList, Map은 AbstractMap으로부터 상속받아 사용한다.**

#### **4. 클래스가 private이거나 package-private이고 equals 메서드를 굳이 호출할 일이 없는 경우**

**equals가 실수로라도 호출되는 걸 막고 싶다면 다음처럼 구현하자.**

```java
@Override
public boolean equals(Object o) {
    throw new AssertionError(); //호출금지!
}
```

* * *

#### **그렇다면 재정의를 해야 하는 경우는 언제일까?**

#### ***재정의를 해야 하는 경우***

  
**객체 식별성(Object Identity : 두 객체가 물리적으로 같은가)이 아니라 논리적 동치성을 확인해야 하는데,**

**상위 클래스의 equals가 논리적 동치성을 비교하도록** **재정의되지 않았을 때이다.**  
  
**주로 값 클래스들이 여기에 해당한다. 값 클래스란 Integer, String처럼 값을 표현하는 클래스를 말한다.**  
**두 값 객체를 equals로 비교하는 프로그래머는 객체가 같은지가 아니라 값이 같은지를 알고 싶어 할 것이다.**  
**이렇게 두 값 클래스의 equals가 논리적 동치성을 확인하도록 재정의해두면, 그 인스턴스는 값을 비교하길 원하는 프로그래머의 기대에 부응하고** **Map 키와 Set의 원소로 사용할 수 있게 된다.** 

**(Map과 Set은 값으로 키로 갖거나 원소로 갖기에 중복을 허용할 수 없다.)**  
  
**그리고 하나 주의할 점은 값 클래스라 해도, 값이 같은 인스턴스가 둘 이상 만들어지지 않음을 보장하는**

**인스턴스 통제 클래스(아이템 1)라면 equals를 재정의하지 않아도 된다.**

  
**Enum도 여기에 해당한다. 이런 클래스에서는 어차피 논리적으로 같은 인스턴스가 2개 이상 만들어지지**

**않으니 논리적 동치성과 객체 식별성이 사실상 똑같은 의미가 된다.**  
**따라서 Object의 equals가 논리적 동치성까지 확인해 준다고 볼 수 있다.**

* * *

#### ***equals 메서드는 동치관계(Equivalence relation)를 구현하며, 다음 5가지 규약을 따라야 한다.***

####   
**1. 반사성(Reflexivity)**

**null이 아닌 모든 참조값 x에 대해, x.equals(x)는 true이다. 즉, 객체는 자기 자신과 같아야 한다는 뜻이다.**

* * *

#### **2\. 대칭성(Symmetry)**

**null이 아닌 모든 참조 값 x, y에 대해, x.equals(y)가 true면 y.equals(x)도 true이다.**

**즉, 두 객체는 서로에 대한 동치 여부에 똑같이 답해야 한다는 뜻이다.**  
  

**CaseInsensitiveString의 equals는 일반 문자열과도 비교를 시도한다. 다음처럼 일반 String 객체가 하나씩 있다고 해보자.**  
 

```java
public class CaseInsensitiveString {
    private final String s;

    public CaseInsensitiveString(String s) {
        this.s = Objects.requireNonNull(s);
    }

    // 대칭성 위배
    @Override
    public boolean equals(Object o) {
        if(o instanceof CaseInsensitiveString) {
            return s.equalsIgnoreCase(((CaseInsensitiveString) o).s);
        }
        if(o instanceof String) {
            return s.equalsIgnoreCase((String) o);
        }
        return false;
    }
}
```

```java
/**
 * 대칭성 위배의 경우
 */
// 대칭성
CaseInsensitiveString cis = new CaseInsensitiveString("Polish");
String s = "polish";

// CaseInsensitiveString는 String의 존재를 알지만, String은 CaseInsensitiveString존재를 모른다.
// 한방향으로만 작동한다. 대칭성을 명백히 위반한다.
System.out.println(cis.equals(s)); // true
System.out.println(s.equals(cis)); // false
```

  
**예상할 수 있듯 cis.equals(s)는 true가 반환된다.**

**문제는 CaseInsensitiveString의 equals는 일반 String을 알고 있지만,**  
**String의 equals는 CaseInsensitiveString의 존재를 모른다는데 있다.**

**따라서 s.equals(cis)는 false를 반환하여, 대칭성을 명백히 위반한다.**

* * *

**이번에는 CaseInsensitiveString을 컬렉션에 넣어보자.**

**해당 부분은 JDK버전마다 반환하는 결과가 다를 것이다. 필자는 true가 나왔다.**  
***equals 규약을 어기면 그 객체를 사용하는 다른 객체들이 어떻게 반응할지 알 수 없다는 것을 보여준다.***

```java
// 컬렉션에 CaseInsensitiveString를 넣어보자.
List<CaseInsensitiveString> list = new ArrayList<>();
list.add(cis);
// 해당 부분은 JDK버전마다 반환하는 결과가 다를것이다. 필자는 true가 나왔다.
// equals 규약을 어기면 그 객체를 사용하는 다른 객체들이 어떻게 반응할지 알수없다는것을 보여준다.
System.out.println(list.contains(cis)); // 알수없음
```

  
   
**이를 해결하기 위해서는 CaseInsensitiveString의 equals를 String과 연동하겠다는 허황된 꿈을 버려야 한다.**

**그 결과 equals는 다음처럼 간단한 모습으로 바뀐다.**

```java
// 대칭성을 위배하는 코드를 수정하기
@Override
public boolean equals(Object o) {
    return o instanceof CaseInsensitiveString && ((CaseInsensitiveString) o).s.equalsIgnoreCase(s);
}
```

```java
System.out.println(cis.equals(s)); // false
System.out.println(s.equals(cis)); // false
```

* * *

#### **3. 추이성(Transitivity)**

**null이 아닌 모든 참조 값 x, y, z에 대해, x.equals(y)가 true이고 y.equals(z)도 true이면** **x.equals(z)도 true이다.**

  
**첫 번째 객체와 두 번째 객체가 같고, 두 번째 객체와 세 번째 객체가 같다면, 첫 번째 객체와 세 번째 객체도 같아야 한다는 뜻이다.**  
  
**만약 상위 클래스에는 없는 새로운 필드를 하위 클래스에 추가하는 상황을 생각해 보자.** 

**equals 비교에 영향을 주는 정보를 추가하는 것이다.**

```java
public class Point {
    private final int x;
    private final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public boolean equals(Object o) {
        if(!(o instanceof Point)) return false;
        Point p = (Point) o;
        return p.x == x && p.y == y;
    }
}
```

```java
public class ColorPoint extends Point {

    private final Color color;
    public ColorPoint(int x, int y, Color color) {
        super(x, y);
        this.color = color;
    }

    /**
     * 코드 10-2 잘못된 코드 - 대칭성 위배! (57쪽)
      */
    @Override
    public boolean equals(Object o) {
        if (!(o instanceof ColorPoint))
            return false;
        return super.equals(o) && ((ColorPoint) o).color == color;
    }
}
```

```java
/**
 * 추이성 위배의 경우
 */
// 첫 번째 equals 메서드(코드 10-2)는 대칭성을 위배한다. (57쪽)
Point p = new Point(1,2);
ColorPoint cp = new ColorPoint(1,2, Color.RED);

System.out.println(p.equals(cp)); // true
System.out.println(cp.equals(p)); // false
```

**결과적으로 대칭성을 만족시키지 못한다. 이유는 Point의 equals()는 ColorPoint를 고려하지 않기 때문이다.**

**그러면 타입까지 고려한 equals()를 어떻게 구현할까? 아래 코드를 보자.**

```java
public class ColorPoint extends Point {

    private final Color color;
    public ColorPoint(int x, int y, Color color) {
        super(x, y);
        this.color = color;
    }

    /**
     * 코드 10-3 잘못된 코드 - 추이성 위배! (57쪽) [대칭성은 해결]
     */
    @Override
    public boolean equals(Object o) {
        if (!(o instanceof Point))
            return false;

        // o가 일반 Point면 색상을 무시하고 비교한다.
        if (!(o instanceof ColorPoint))
            return o.equals(this);

        // o가 ColorPoint면 색상까지 비교한다.
        return super.equals(o) && ((ColorPoint) o).color == color;
    }
}
```

```java
// 두 번째 equals 메서드(코드 10-3)는 추이성을 위배한다. [대칭성은 해결] (57쪽)
ColorPoint p1 = new ColorPoint(1, 2, Color.RED);
Point p2 = new Point(1, 2);
ColorPoint p3 = new ColorPoint(1, 2, Color.BLUE);
System.out.printf("%s %s %s%n", p1.equals(p2), p2.equals(p3), p1.equals(p3)); // true, true, false
```

**p1과 p2, p2와 p3 비교에서는 색상을 무시했지만, p1과 p3 비교에서는 색상까지 고려했다.** 

  
**equals 메서드는 어떻게 해야 할까? 그대로 둔다면 Point의 구현이 상속되어 색상 정보는 무시한 채****비교를 수행한다. equals 규약을 어긴 것은 아니지만, 중요한 정보를 놓치게 되니 받아들일 수 없는 상황이다.**

**사실 이 현상은 모든 객체 지향 언어의 동치관계에서 나타나는 근본적인 문제이다.**

**구체 클래스를 확장해 새로운 값을 추가하면서 equals 규약을 만족시킬 방법은 존재하지 않는다.**  
**객체 지향적 추상화의 이점을 포기하지 않는 한은 말이다.**

  
**이 말은 얼핏, equals 안의 instance 검사를 getClass 검사(즉, 같은 클래스의 객체와 비교할 때만 true)로**

**바꾸면 규약도 지키고 값도 추가하면서 구체 클래스를 상속할 수 있다는 뜻으로 들린다.**

**다른 구현의 예시를 보자.**

**Point를 상속받은 CpunterPoint 만들고 부모 클래스의 equals() 메서드를 그대로 사용한다.**

**만약 아래 코드의 결과가 나와야 좋은 코드일까?**

```java
public class Point {
    private final int x;
    private final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    // 그렇다면 해결방법은?
    // 사실상 구체 클래스를 확장해 새로운 값을 추가하면서 equals 규약을 만족시킬 수는 없다.
    // 그렇다면 getClass 검사로 바꾸면 어떻게 될까
    // 리스코프 치환 원칙 위배
    /**
     * 코드 10-4 잘못된 코드 - 추이성 위배! (58쪽)  - LSP 위배
     */
    @Override public boolean equals(Object o) {
        if (o == null || o.getClass() != getClass())
            return false;
        Point p = (Point) o;
        return p.x == x && p.y == y;
    }
    // 위 코드는 같은 구현 클래스의 객체와 비교할 때만 true를 반환한다. 하지만, 실제로 활용할 수는 없다. Point의 하위 클래스는 정의상 여전히 Point이므로 어디서든 Point로써 활용될 수 있어야하는데 그렇지 못하기 때문이다.

    // 객체 비교를 위한 hashCode 작성
    @Override public int hashCode()  {
        return 31 * x + y;
    }
}
```

```java
public class CounterPoint extends Point{
    private static final AtomicInteger counter = new AtomicInteger();

    public CounterPoint(int x, int y) {
        super(x, y);
        counter.incrementAndGet();
    }

    public static int numberCreated() {
        return counter.get();
    }
}
```

```java
// 단위 원 안의 모든 점을 포함하도록 unitCircle을 초기화한다. (58쪽)
private static final Set<Point> unitCircle = Set.of(
        new Point( 1,  0), new Point( 0,  1),
        new Point(-1,  0), new Point( 0, -1));

public static boolean onUnitCircle(Point p) {
    return unitCircle.contains(p);
}

// 세 번째 equals 메서드(코드 10-4)는 리스코프 치환 원칙(59쪽) 위배
Point p4 = new Point(1,  0);
Point p5 = new CounterPoint(1, 0);

// true를 출력한다.
System.out.println(onUnitCircle(p4)); // true

// true를 출력해야 하지만, Point의 equals가 getClass를 사용해 작성되었다면 그렇지 않다.
System.out.println(onUnitCircle(p5)); // false
```

**p4와 p5 모두 true라는 값이 나와야 객체 지향적으로 올바른 코드이다. 하지만 결과적으로는 그렇지 못했고 LSP(리스코프 치환 원칙)을 위배하게 된다.**

**LSP를 지킨다면, 어떤 타입에 있어 중요한 속성이라면 그 하위 타입에서도 마찬가지로 중요하다.**

**따라서 그 타입의 모든 메서드가 하위 타입에서도 똑같이 잘 작동해야 한다.**

  
**즉, Point의 하위 클래스는 여전히 Point 클래스이므로 어디서든 Point로 활용될 수 있어야 한다.**  
**하지만 위 결과처럼 LSP를 위반하게 된다.**

> **LSP \[리스코프 치환 원칙\] : 상위 클래스를 사용하고 있는 곳에 슈퍼 클래스를 사용하더라도 전체적인 컨텍스트가  
> 바뀌지 않아야 한다. 즉, 상속받은 자식 클래스는 상속받은 부모 클래스를 대신할 수 있어야 한다.**

**상속으로는 추이성을 만족하기 어렵다.**

**상속구조로 클래스를 만들어나가면, 고민해야 할 것이 많아지기 때문에 equals() 구현하기 어려워진다.**

**상속을 사용하는 경우 다음 2가지 경우로 나누어 정리해 볼 수 있다.**

> **1\. 서브 클래스에 필드가 추가되지 않음 -> 상위 클래스의 equals() 그대로 사용**  
> **2\. 서브 클래스에 필드 추가 -> equals 규약을 만족하는 equals()를 추가하는 방법이 없음**

**따라서 만약 equals()를 구현해야 하는 경우라면 상속 대신 Composition을 이용하는 것이 권장된다.**

**서브 클래스에서 필드가 추가되어야 하며, equals()가 필요한 경우라면 무조건 Composition을 이용해야 한다.**

```java
import java.awt.*;
import java.util.Objects;

public class ColorPoint {

    private final Point point;
    private final Color color;

    public ColorPoint(int x, int y, Color color) {
        point = new Point(x, y);
        this.color = Objects.requireNonNull(color);
    }

    /**
     * 이 ColorPoint의 Point 뷰를 반환한다.
     */
    public Point asPoint() {
        return point;
    }

    @Override
    public boolean equals(Object o) {
        if (!(o instanceof ColorPoint))
            return false;
        ColorPoint cp = (ColorPoint) o;
        return cp.point.equals(point) && cp.color.equals(color);
    }

    @Override public int hashCode() {
        return 31 * point.hashCode() + color.hashCode();
    }
}
```

**Composition으로 특정 클래스를 구현한 경우, equals()를 구현하기 쉬어진다.**

**자신의 타입이 아닌 경우면 false를 리턴 후, 주요 필드의 equals()를 모두 확인하는 것이다.**

**또한 Composition으로 구현했을 때,**

**asPoint() 같은 메서드를 이용해 View를 제공해 줄 수 있다는 장점이 있다.**

**여기서 *asPoint는 ColorPoint이지만, 필요하다면 Point로 볼 수 있게 해주는 기능*이다.**

```java
// 네번째 Composition을 이용하여 equals 메서드(코드 10-4)는 리스코프 치환 원칙(59쪽) 해결
Point p6 = new Point(1, 0);
Point p7 = new ColorPoint(1, 0, Color.RED).asPoint();

// true
System.out.println(onUnitCircle(p6));

// true
System.out.println(onUnitCircle(p7));
```

**결과적으로 Composition을 사용하면, 추이성 + 반사성 + 대칭성을 손쉽게 만족하는 equals() 구현할 수 있다.**

* * *

####   
**4. 일관성(Consistency)**

**null이 아닌 모든 참조값 x, y에 대해, x.equals(y)를 반복해서 호출하면 항상 true를 반환하거나 항상 false를 반환한다.** **즉, 두 객체가 같다면 앞으로도 영원히 같아야 한다는 뜻이다.**  
  
**가변 객체는 비교 시점에 따라 서로 다를 수도 혹은 같을 수도 있지만, 불변 객체는 그렇지 않다.**  
**하지만, 클래스가 불변이든 가변이든 equals의 판단에 신뢰할 수 없는 자원이 끼어들게 해서는 안된다.**

  
**예컨대 java.net.URL의 equals는 주어진 URL과 매핑된 호스트의 IP 주소를 이용해 비교한다.**  
**호스트 이름을 IP 주소로 바꾸려면 네트워크를 통해야 하는데 그 결과가 항상 같다고 보장할 수 없다.**

```java
/**
 * 일관성 위배인 경우
 */
// 일관성 위배 가능성 있음. P61
URL naver1 = new URL("https", "about.naver", "/webtoon/");
URL naver2 = new URL("https", "about.naver", "/webtoon/");
System.out.println(naver1.equals(naver2));
```

* * *

#### **5\. null 아님**

**null 아님은 모든 객체가 null과 같지 않아야 한다는 뜻이다. 의도하지 않았지만 발생할 경우 NPE(NullPointerException)을 던지게 될 것이다.**

  
**다음 코드처럼 입력이 null인지를 확인하여 자신을 보호한다.**

**null을 해결하기 위해 명시적 방법과 묵시접 방법이 있으며, 묵시적 방법을 사용하는 것이 바람직하다.**  
  

```java
// 명시적 null 검사
@Override
public boolean equals(Object o) {
    if (o == null)
        return false;
}

// 묵시적 null 검사
@Override
public boolean equals(Object o) {
    if (!(o instanceof MyType))
        return false;
    MyType mt =(MyType) o;
}
```

* * *

#### **equals를 다 구현했다면 세 가지만 자문해 보자.**  
  

**대칭적인가? 추이성이 있는가? 일관적인가?**

**아래 코드는 이상의 비법에 따라 작성해 본 PhoneNumber 클래스이다. 즉, 전형적인 equals 메서드이다.**

```java
public class PhoneNumber {
    private final short areaCode, prefix, lineNum;

    public PhoneNumber(short areaCode, short prefix, short lineNum) {
        this.areaCode = areaCode;
        this.prefix = prefix;
        this.lineNum = lineNum;
    }
    
    private static short rangeCheck(int val, int max, String arg) {
        if(val < 0 || val > max) {
            throw new IllegalArgumentException(arg + ": " + val);
        }
        return (short) val;
    }
    
    @Override
    public boolean equals(Object o) {
        if(o == this) {
            return true;
        }
        if(!(o instanceof PhoneNumber)) {
            return false;
        }
        PhoneNumber pn = (PhoneNumber) o;
        return pn.lineNum == lineNum && pn.prefix == prefix && pn.areaCode == areaCode;
    }
```

* * *

#### **마지막 주의사항**

-   **equals를 재정의할 땐 hashCode도 반드시 재정의 하자. (아이템 11)**
-   **너무 복잡하게 해결하려 들지 말자.**  
    **→ 필드들의 동치성만 검사해도 equals 규약을 어렵지 않게 지킬 수 있다.**
-   **Object 외의 타입을 매개변수로 받는 equals 메서드는 선언하지 말자.**

```java
// 잘못된 예 - 입력 타입은 반드시 Object여야 한다.
public boolean equals(MyClass o) {
...
}
```
