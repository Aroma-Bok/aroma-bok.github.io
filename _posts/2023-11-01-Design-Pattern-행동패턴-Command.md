---
title: "[Design Pattern] 행동패턴 - Command"
date: 2023-11-01 10:28:43 +0900
categories: ["Language", "Design Pattern"]
tags: ["designpattern command", "java command", "디자인패턴 커맨드", "디자인패턴 커맨드패턴", "자바 커맨드패턴"]
tistory_url: "https://aroma-bok.tistory.com/entry/Design-Pattern-%ED%96%89%EB%8F%99%ED%8C%A8%ED%84%B4-Command"
---
### **커맨드 (Command) 패턴**

-   **요청을 캡슐화하여 호출자(Invoker)와 수신자(Receiver)를 분리(의존성 제거) 하는 패턴**

#### **사용하는 이유?**

-   **호출자(Invoker) 객체가 수신자 (Receiver) 객체의 기능을 직접 호출한다면, 호출자에서 응답자 메소드를 호출하는**
-   **부분을 수정하거나 응답자의 코드가 수정되면 한쪽이 수정되더라도 양쪽 모두 수정이 되는 의존도가 높은 상태이다.**
-   **즉, 이 2개의 객체간 의존성을 제거하고 유연성을 확복하기 위함이다.**

#### **구조**

-   **Invoker : Command에 기능을 실행해달라고 요청하는 호출자 클래스**
-   **Command : ConcreateCommand의 실행 기능에 대한 인터페이스로 실행은 execute, 취소는 undo 같은 메소드로 선언**
-   **ConcreateCommand : 실제로 실행되는 기능을 구현**
-   **Receiver : ConcreateCommand에서 기능을 구현하기위해 필요한 클래스**

![](/assets/img/posts/242/1.png)

* * *

#### **커맨드 (Command) 패턴 적용 전**

```java
public class Button { // 호출자 - Invoker
    private Light light;

    public void press() {
        light.on();
    }

    public Button(Light light) {
        this.light = light;
    }
}
```

```java
public class Light { // 수신자 - Receiver
    private boolean isOn;

    public void on() {
        System.out.println("불을 켭니다.");
        this.isOn = true;
    }
}
```

-   **Button이 호출자(Invoker)가 되어 Light 객체 (Receiver)의 기능을 호출한다**

```java
    @Test
    void test1() {
        Button button = new Button(new Light());
        button.press();
    }
```

* * *

**이 상황에서 "불을 끕니다." 라는 문구를 표시하는 새로운 메소드를 추가하려면 어떻게 될까?**  
 

-   **요구사항으로 인한 Receiver 객체 코드 수정**

```java
public class Light { // 수신자 - Receiver
    private boolean isOn;

    public void on() {
        System.out.println("불을 켭니다.");
        this.isOn = true;
    }

    // 요구사항으로 새로운 메소드 추가 -> 코드 수정 발생
    public void off() {
        System.out.println("불을 끕니다.");
        this.isOn = false;
    }
}
```

-   **요구사항으로 Invoker 객체 코드 수정**

```java
public class Button { // 호출자 - Invoker
    private Light light;

    public void press() {
//        light.on();
        // '불을 끈다' 라는 요구사항으로 코드 수정 필요
        light.off();
    }

    public Button(Light light) {
        this.light = light;
    }
}
```

   
**즉, 2개의 객체가 모두 수정이 필요한 상황이다. 호출자(Invoker)와 수신자(Receiver) 간의 의존성을 제거하기 위해**  
**커맨드 (Command) 패턴을 사용한다.**

* * *

#### **커맨드 (Command) 패턴 적용 후**

-   **실행되는 행위를 캡슐화**

-   **버튼을 누를 때 어떤 행동을 할 지, Command 인터페이스를 통해 호출**
-   **행위를 추상화하고, 내부 기능을 캡슐화 함으로써 호출자(Invoker)는 수신자(Receiver)의 구체적인 사항을 몰라도**  
    **된다. (의존성 분리)**

```java
public interface Command {
    void execute();
}
```

-   **Command을 구현하는 캡슐화 객체**

```java
public class LightOnCommand implements Command{
    private Light light;

    @Override
    public void execute() {
        light.on();
    }

    public LightOnCommand(Light light) {
        this.light = light;
    }
}
```

```java
public class LightOffCommand implements Command{
    private Light light;
    
    @Override
    public void execute() {
        light.off();
    }

    public LightOffCommand(Light light) {
        this.light = light;
    }
}
```

-   **호출자(Invoker)는 Command의 'execute' 메소드만 바라보고 구체적인 기능은 내부로 캡슐화 되어있다.**
-   **요구사항이 추가되거나 수정이되면 수신자(Receiver)의 구현체가 추가되거나 수정된다.**
-   **기능이 변경되더라도 호출자(Invoker)의 Command를 실행하는 부분은 수정이 일어나지 않는다.**
-   **즉, 수정 범위의 축소 그리고 Command 재사용성의 증가, 책임의 분리가 된다.**

```java
public class Button2 { // 호출자 - Invoker
    // Command 객체 이용
    private final Command command;

    public Button2(Command command) {
        this.command = command;
    }

    public void press() {
        command.execute();
    }
}
```

* * *

#### **커맨드 (Command) 패턴**

#### **장점**

-   **기존 코드를 변경하지 않고 새로운 커맨드를 만들 수 있다.**
-   **수신자(Receiver)의 코드가 변경되어도 호출자(Invoker)의 코드는 변경되지 않는다.**
-   **커맨드 객체를 로깅, DB에 저장 및 네트워크로 전송하는 등 다양한 방법으로 활용할 수 있다**

#### **단점**

-   **클래스가 많아진다.**
