---
title: "[Design Pattern] 생성패턴 - ProtoType"
date: 2023-10-19 13:40:53 +0900
categories: ["Language", "Design Pattern"]
tags: ["designpattern prototype", "디자인패턴 프로토타입", "디자인패턴 프로토타입 패턴 백기선", "디자인패턴 프로토타입 패턴 코드", "얕은복사 깊은복사", "프로토타입 백기선", "프로토타입 코드"]
tistory_url: "https://aroma-bok.tistory.com/entry/Design-Pattern-%EC%83%9D%EC%84%B1%ED%8C%A8%ED%84%B4-ProtoType"
---
### **ProtoType - 프로토타입** 

-   **기존 인스턴스를 복제해서 새로운 인스턴스를 만드는 패턴**
-   **일반적으로 기존 인스턴스를 응용해서 새로운 인스턴스를 생성할 때 유용하게 사용**
-   **인스턴스를 생성할 때마다 DB를 거쳐서 인스턴스를 생성한다면 많은 자원을 소모할 것이다.**  
    **대신에 기존 인스턴스를 복제해 원하는 값만 변경해서 사용한다면 자원의 소모를 줄일 수 있다.**
-   **Java가 제공하는 clone()을 사용해서 간단하게 구현 가능**

* * *

#### **프로토타입 패턴 적용 전**

-   **이름과 국적을 가지고 있는 선수 클래스 그리고 DB를 통해서 데이터를 가지고 오는 메소드가 있다**

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Player {
    private String name;
    private String country;

    // 선수 정보를 DB에서 얻어온다고 가정
    public void loadPlayerDataFromDB() {
        name = "Ramos";
        country = "Spain";
    }
}
```

-   **팀 이름과 선수를 포함하는 팀 클래스가 있다.**

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Team {
    private String name;
    private Player player;

}
```

-   **새로운 플레이어를 만들기 위해서는 플레이어를 생성하고 DB에서 데이터를 가져오는 작업을 선행 후 플레이어를**  
    **포함하는 팀을 만들 수 있다.**

```java
    @Test
    void test1() {
        Player player = new Player();
        player.loadPlayerDataFromDB();

        Team realMadrid = new Team("realMadrid", player);
        System.out.println(realMadrid);
    }
```

> **무엇이 문제인가?**  
>   
> **이후에 다른 팀 객체를 만들려고 한다면 똑같은 순서를 반복하면서 객체를 생성해야 한다.  
> (비용적인 측면..)**

* * *

#### **프로토타입 패턴 적용 후**

-   **프로토타입 패턴은 Java가 제공하는 clone을 사용해서 간단하게 구현 가능하다. clone은  기본적으로 인스턴스의  
    얕은 복사를 제공하는 메소드로 최상위 클래스인 Object에 선언되어 있다.**
-   **protected로 선언되어 있어서, 아무 클래스나 clone을 제공을 하는 것이 아닌 명시적으로 clone이 가능하도록 만든  
    클래스만 clone을 외부에 제공할 수 있다.**

```java
public class Object {
	... 생략
    
    @HotSpotIntrinsicCandidate
    protected native Object clone() throws CloneNotSupportedException;
    
    ... 생략
}
```

> **default : 동일한 패키지 내에서만 접근가능**  
> **public  : 접근 제한 없음**  
> **protected : 동일한 패키지 내에서 접근 혹은 파생 클래스에서만 접근 가능**  
> **private : 자기 자신의 클래스 내에서만 접근 가능**

-   **명시적으로 clone이 가능하도록 만든 클래스 = Cloneable Interface를 구현하고 clone 메소드를 @Override**

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@EqualsAndHashCode // 값 비교를 위해 사용
public class Team implements Cloneable{
    private String name;
    private Player player;

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```

-   **리플렉션을 통해서 clone()에 접근**

```java
    @Test
    void test2() throws CloneNotSupportedException {
        Player player = new Player();
        player.loadPlayerDataFromDB();

        Team realMadrid = new Team("realMadrid", player);
        Team copyRealMadrid = ReflectionTestUtils.invokeMethod(realMadrid,"clone");

        System.out.println("같은 인스턴스인가? " + (realMadrid == copyRealMadrid));
        System.out.println("같은 player 인스턴스를 가지고 있는가? " + (realMadrid.getPlayer() == copyRealMadrid.getPlayer()));
        System.out.println("같은 값을 가지고 있는가? " + realMadrid.getPlayer().equals(copyRealMadrid.getPlayer()));

        assertThat(realMadrid).isEqualTo(copyRealMadrid);
    }
```

-   **기대했던 결과처럼 다른 인스턴스이면서 같은 값을 지닌 새로운 인스턴스를 생성했다.**

```java
같은 인스턴스인가? false
같은 player 인스턴스를 가지고 있는가? true
같은 값을 가지고 있는가? true
```

> **참고**  
>   
> **Object가 제공하는 clone은 얕은 복사 (Shallow Copy)를 사용하려면 super.clone() 그대로 사용하면 된다.**  
> **만약 깊은 복사 (Deep Copy)를 사용하려면 clone()을 재정의해서 사용하면 된다.  
> ****그러면 내부 객체도 다른 인스턴스를 참조하고 있는 것을 볼 수 있다.**

```java
    @Override
    protected Object clone() throws CloneNotSupportedException {
        Player copyPlayer = new Player();
        player.loadPlayerDataFromDB();

        Team copyTeam = new Team();
        copyTeam.setName(this.name);
        copyTeam.setPlayer(copyPlayer);

        return copyTeam;
    }
```

```java
같은 인스턴스인가? false
같은 player 인스턴스를 가지고 있는가? false
같은 값을 가지고 있는가? false
```

* * *

#### **프로토 타입 (Prototype)**

**장점**

-   **복잡한 객체를 만드는 과정을 숨길 수 있다.**
-   **원본 객체를 복제하는 과정이 새 인스턴스를 만드는 것보다 비용(자원)적인 면에서 효율적일 수 있다.**
-   **추상적인 타입을 리턴할 수 있다. (clone() 메소드를 커스텀하게 정의할 수 있으므로)**

**단점**

-   **복제한 객체를 만드는 과정(clone 과정 자체) 자체가 복잡할 수 있다.**

* * *

**++추가 내용**

**clone(), =(할당 연산자) 두 개 다 얕은 복사(Shallow Copy)인데 차이점이 있나?**

**정리해 둔 얕은 복사(Shallow Copy) vs 깊은 복사(Deep Copy) 개념도 알아두고 공부하면 좋을 것 같다.**

[2023.10.19 - \[Language/Java\] - \[Java\] 얕은 복사 (Shallow Copy) vs 깊은 복사 (Deep Copy)](https://aroma-bok.tistory.com/entry/Java-%EC%96%95%EC%9D%80-%EB%B3%B5%EC%82%AC-Shallow-Copy-vs-%EA%B9%8A%EC%9D%80-%EB%B3%B5%EC%82%AC-Deep-Copy)

> **Clone() 메서드를 사용하면 기본적으로 얕은 복사 (Shallow Copy)를 기본적으로 한다는데  
> 그러면 '= (할당 연산자)'를 통한 복제와는 뭐가 다른지 이해가 잘 안 갔다.**  
>   
> **할당 연산자로 복제해서 얻은 값을 변경하면 원본객체도 변경되었지만, clone()을 통해서 얻은 객체를 변경하면  
> 원본객체는 변경되지 않았다. 같은 얕은 복사(Shallow Copy)라면서 왜 다를까?**   
>   
> **정리하면 이렇다.**  
> **clone() 메소드를 사용하여 객체를 복제하면 새로운 객체가 생성되지만, 얕은 복사를 수행하므로  
> 새로운 객체와 원본 객체가 서로 다를 수 있지만 내부 객체에 대한 참조는 같다.  
> 즉, 내부 객체에 대한 변경은 두 객체 모두에 영향을 미칠 수 있다.**  
>   
> **할당 연산자를 사용하여 새로운 객체를 만들면, 동일한 객체를 가리키는 새로운 참조를  
> 만드는 것이므로 객체의 변경은 원본 객체와 새로운 참조 양쪽에 모두 영향을 미칠 것입니다.**
