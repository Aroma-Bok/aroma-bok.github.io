---
title: "[Design Pattern] 행동패턴 - Memento"
date: 2023-11-08 15:49:40 +0900
categories: ["Language", "Design Pattern"]
tags: ["java memento", "디자인패턴 memento", "디자인패턴 메멘토", "자바 메멘토"]
tistory_url: "https://aroma-bok.tistory.com/entry/Design-Pattern-%ED%96%89%EB%8F%99%ED%8C%A8%ED%84%B4-Memento"
---
### **메멘토(Memento) 패턴**

-   **캡슐을 유지하면서 객체 내부 상태를 외부에 저장하는 방법**
-   **객체의 상태정보를 가지는 클래스를 따로 생성하여 객체 내부의 상태를 외부에 저장해 놓고 저장해 놓은**
-   **정보를 복원시킬 수 있도록 해주는 패턴**
-   **'특정 시점'의 '객체의 상태'를 외부에 저장하기 위해서는 객체 내부의 상태가 외부에 온전히 노출되어야 하지만**
-   **메멘토 패턴을 사용한다면 상태를 지키면서 복원시킬 수 있다.**
-   **즉, 메멘토 패턴은 내부 상태를 전부 외부에 노출하지 않으면서 객체 상태를 외부에 저장했다가 해당 상태로 다시 복구시키는 목적이 있다. (저장된 시점 불러오기와 같은 상태를 다시 불러올 때 사용)**

#### **구조**

-   **Originator : 상태를 저장하고 복원하려는 객체**
-   **Memento :  immutable 한 객체로 일정 시점의 Originator 상태를 저장하는 객체**
-   **Caretaker : Originator 상태를 Memento 타입으로 저장하고 관리하는 객체**

![](/assets/img/posts/246/1.png)

* * *

#### **메멘토(Memento) 패턴 적용 전**

-   **특정한 상태값을 가지는 Game 객체를 선언**
-   **객체의 상태값은 계속 변경되고, 특정한 시점을 외부에 저장해 두었다가 복원시키고자 한다.**

```java
@Getter
@Setter
public class GameBefore {
    private int blueTeamScore = 0;
    private int redTeamScore = 0;
}
```

```java
    @Test
    void test1() {
        GameBefore game = new GameBefore();
        game.setBlueTeamScore(10);
        game.setRedTeamScore(20);

        // 클라이언트가 점수 저장
        int blueTeamScore = game.getBlueTeamScore();
        int redTeamScore = game.getRedTeamScore();

        // 값이 변경됨
        game.setBlueTeamScore(15);
        game.setRedTeamScore(28);

        // 저장된 시점으로 복구
        GameBefore restoreGame = new GameBefore();
        restoreGame.setBlueTeamScore(blueTeamScore);
        restoreGame.setRedTeamScore(redTeamScore);
        System.out.println("블루팀 점수 : " + restoreGame.getBlueTeamScore());
        System.out.println("레드팀 점수 : " + restoreGame.getRedTeamScore());
    }
```

   
**특정 객체의 상태값이 어떤 걸 가지고 있는지 외부에 전부 노출되고 있다.**  
**객체 상태정보를 은닉하기 위해 접근제어자를 private으로 사용한 의미가 없어진다.**

* * *

#### **메멘토(Memento) 패턴 적용 후**

-   **저장하고자 하는 객체의 상태값을 알고 있는 별도의 객체를 만들어서 관리**
-   **Memento가 저장하고자 하는 객체(Originator)의 상태를 알고 있어야 한다**
-   **외부 클라이언트에서 객체의 상태값을 알고 있는 단점을 줄일 수 있다**

-   **Memento는 상태값이 변경되어서는 안 되기 때문에 불변 객체이어야 한다.**

```java
@Getter
public class GameSave {
    private final int blueTeamScore;
    private final int redTeamScore;

    public GameSave(int blueTeamScore, int redTeamScore) {
        this.blueTeamScore = blueTeamScore;
        this.redTeamScore = redTeamScore;
    }
}
```

-   **Originator 객체에서 특정 상태값을 저장할 시, Memento 객체를 반환**

```java
@Getter
public class GameSave {
    private final int blueTeamScore;
    private final int redTeamScore;

    public GameSave(int blueTeamScore, int redTeamScore) {
        this.blueTeamScore = blueTeamScore;
        this.redTeamScore = redTeamScore;
    }
}
```

```java
 @Test
    void test2() {
        Game game = new Game();
        // 값 세팅
        game.setBlueTeamScore(30);
        game.setRedTeamScore(15);

        // 저장
        GameSave save = game.save();
        // 변경
        game.setBlueTeamScore(18);
        game.setRedTeamScore(10);

        // 복원
        game.restore(save);
        System.out.println("블루팀 점수 : " + game.getBlueTeamScore());
        System.out.println("레드팀 점수 : " + game.getRedTeamScore());
    }
```

```java
블루팀 점수 : 30
레드팀 점수 : 15
```

* * *

#### **메멘토(Memento) 패턴**

#### **장점**

-   **캡슐화를 지키면서 상태 객체의 상태 스냅샷을 만들 수 있다.**
-   **객체 상태를 저장하고 또는 복원하는 역할을 CareTaker (다수의 Memento 상태를 유지하는 객체)에게 위임 가능**
-   **객체 상태가 바뀌어도 클라이언트 코드는 변경되지 않는다.**

#### **단점**

-   **Memento를 자주 생성하는 경우 메모리 사용량에 많은 영향을 줄 수 있다.**
-   **Originator 객체의 저장해야 할 상태정보가 커서 Memento 객체가 커지면 많은 메모리를 잡아먹을 수 있다.**
