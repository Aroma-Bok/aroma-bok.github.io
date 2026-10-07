---
title: "[Design Pattern] 행동패턴 - Mediator"
date: 2023-11-07 11:13:32 +0900
categories: ["Language", "Design Pattern"]
tags: ["designpattern mediator", "java mediator", "디자인패턴 mediator", "디자인패턴 자바 중재자", "디자인패턴 중재자", "자바 중재자"]
tistory_url: "https://aroma-bok.tistory.com/entry/Design-Pattern-%ED%96%89%EB%8F%99%ED%8C%A8%ED%84%B4-Mediator"
---
### **중재자 (Mediator) 패턴**

-   **객체들 간의 의사소통 하는 방법을 캡슐화하여 객체 간의 의존성(결합도)을 낮추는 패턴**
-   **객체 간의 중간 의사소통 담당자(중재자)를 통해 객체 간의 소통을 캡슐화하는 방법**
-   **객체 간의 직접적인 상호작용보다 중자를 두어 간접적으로 상호작용 하도록 하는 것**
-   **컴포넌트 사이의 M:N의 의존관계가 중재자와의 M:1의 의존관계가 되므로 결합도 감소**

#### **구조**

-   **Mediator : Colleague 객체 간의 상호참조를 위한 인터페이스. 클라이언트 등록, 실행등의 메소드 정의**
-   **Colleague : 다른 Colleague와의 상호참조를 위한 인터페이스**
-   **ConcreteMediator : Mediator 구현 클래스, Colleague 간의 상호참조를 조정**
-   **ConcreteColleage : Colleage 구현 클래스, Mediator를 통해 다른 Colleage와의 상호참조**

![](/assets/img/posts/245/1.png)

-   **Mediator는 Colleague와 상호작용하기 위한 인터페이스를 제공**
-   **ConcreteMediator는 이 인터페이스를 구현**
-   **하위 Colleague는 Mediator를 통해 다른 Colleague와 상호작용 가능**

* * *

#### **중재자 (Mediator) 패턴 사용 예시**

-   **웹 채팅을 하는 시나리오 프로그램**

-   **중개자 인터페이스**
-   **User를 추가하거나 제거하고 각 User에게 메시지를 보낸다.**

```java
public interface Mediator {

    void addUser(Colleague user);
    void deleteUser(Colleague user);
    void sendMessage(String message, Colleague user);
}
```

-   **중재자 인터페이스를 구현한 클래스**
-   **User를 관리하며 각 User에게 메시지를 전송**

```java
public class ConcreteMediator  implements Mediator {

    private final List<Colleague> users;

    public ConcreteMediator() {
        this.users = new ArrayList<>();
    }

    @Override
    public void addUser(Colleague user) {
        this.users.add(user);
        sendMessage("System > " + user.getName() + "님께서 입장하셨습니다.", null);
    }

    @Override
    public void deleteUser(Colleague user) {
        this.users.remove(user);
        sendMessage("System > " + user.getName() + "님께서 퇴장하셨습니다.", null);
    }

    @Override
    public void sendMessage(String message, Colleague user) {
        for(Colleague _user : this.users) {
            if(_user != user) _user.receive(message);
        }
    }
}
```

-   **User를 추상화한 클래스**
-   **중재자를 멤버변수로 가지며, 메시지를 전송하거나 수신받을 수 있다.**

```java
@Getter
@Setter
public abstract class Colleague {
    private Mediator mediator;
    private String name;

    public Colleague(Mediator mediator, String name) {
        this.mediator = mediator;
        this.name = name;
    }

    public abstract void send(String message);
    public abstract void receive(String message);
}
```

-   **추상화된 User 클래스를 구현한 클래스**
-   **메시지의 전송은 중재자에게 전송하는 것을 볼 수 있다.**

```java
public class ConcreteColleague extends Colleague {

    public ConcreteColleague(Mediator mediator, String name) {
        super(mediator, name);
    }

    @Override
    public void send(String message) {
        System.out.println("발신 > " + this.getName() + " : " + message);
        getMediator().sendMessage(message, this);
    }

    @Override
    public void receive(String message) {
        System.out.println("수신 > " + this.getName() + " : " + message);
    }
}
```

-   **채팅을 하는 클래스**
-   **중재자와 각 User를 생성하고, user1 객체가 메시지를 전송하면 중재자를 통해 모든 User에게 메시지 전송**

```java
    @Test
    void test1() {
        Mediator mediator = new ConcreteMediator();

        Colleague user1 = new ConcreteColleague(mediator, "A");
        Colleague user2 = new ConcreteColleague(mediator, "B");
        Colleague user3 = new ConcreteColleague(mediator, "C");
        Colleague user4 = new ConcreteColleague(mediator, "D");

        mediator.addUser(user1);
        mediator.addUser(user2);
        mediator.addUser(user3);
        mediator.addUser(user4);

        user1.send("안녕하세요");

        mediator.deleteUser(user3);
    }
```

-   **로그를 통해서는 모든 메시지가 출력되지만, 실제로는 유저마다 메시지가 출력**

```java
수신 > A : System > A님께서 입장하셨습니다.
수신 > A : System > B님께서 입장하셨습니다.
수신 > B : System > B님께서 입장하셨습니다.
수신 > A : System > C님께서 입장하셨습니다.
수신 > B : System > C님께서 입장하셨습니다.
수신 > C : System > C님께서 입장하셨습니다.
수신 > A : System > D님께서 입장하셨습니다.
수신 > B : System > D님께서 입장하셨습니다.
수신 > C : System > D님께서 입장하셨습니다.
수신 > D : System > D님께서 입장하셨습니다.
발신 > A : 안녕하세요
수신 > B : 안녕하세요
수신 > C : 안녕하세요
수신 > D : 안녕하세요
수신 > A : System > C님께서 퇴장하셨습니다.
수신 > B : System > C님께서 퇴장하셨습니다.
수신 > D : System > C님께서 퇴장하셨습니다.
```

   
   
**중재자 (Mediator) 패턴은 하나의 중재자를 통해 M:1로 연결되어 있기 때문에 전체적인 연결 관계를 파악하기 쉬운 반면에**  
**중재자가 특정 애플리케이션의 로직에 맞춰 만들어지기 때문에 재사용하기 어렵다는 단점이 있다.**

* * *

#### **중자재 (Mediator) 패턴** 

#### **장점**

-   **Colleague 코드를 변경하지 않고 새로운 중재자(Mediator)를 만들어 사용할 수 있다.**
-   **각각의 Colleague 코드를 보다 간결하게 유지할 수 있다.**

#### **단점**

-   **중재자(Mediator) 역할을 하는 클래스의 복잡도와 결합도가 증가 (의존성을 한 곳에 두기 때문에)**
-   **특정 애플리케이션의 로직에 맞춰 만들어지기에 재사용이 어렵다**
