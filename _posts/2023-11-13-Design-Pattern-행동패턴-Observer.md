---
title: "[Design Pattern] 행동패턴 - Observer"
date: 2023-11-13 14:20:40 +0900
categories: ["Language", "Design Pattern"]
tags: ["java observer", "디자인패턴 observer", "디자인패턴 옵저버", "디자인패턴 자바 옵저버", "자바 observer", "자바 옵저버"]
tistory_url: "https://aroma-bok.tistory.com/entry/Design-Pattern-%ED%96%89%EB%8F%99%ED%8C%A8%ED%84%B4-Observer"
---
### **옵저버 (Observer) 패턴**

-   **다수의 객체가 특정 객체 상태 변화를 감지하고 알림을 받는 패턴**
-   **객체의 상태 변화를 관찰하는 관찰자 객체를 생성하여, 특정한 객체의 상태 변경을 지켜보는 디자인 패턴**
-   **객체의 상태변화를 관찰하는 옵저버(관잘차)들의 목록을 객체에 등록하여 상태 변화가 있을 때마다,**
-   **notify를 통해 객체가 직접 목록의 각 옵저버에 통지하고, 각 옵저버는 이벤트가 발생했을 시 동작을 수행한다.**
-   **이러한 구조로 객체의 상태가 변화하면, 종속 객체들이 자동으로 변화가 통지되어 그에 따른 명령을 수행하도록 하는**
-   **1:N (One To Many)의 의존성을 정의해 준다.**
-   **즉, 관찰하고 있는 객체의 상태변경에 실시간으로 대응하기 위한 목적을 가지는 패턴**
-   **"상태가 변화되는 객체"와 이 객체의 상태에 "의존성이 있는 객체" -> 두 객체가 직접적으로 참조되지 않고 중간에**
-   **관찰자 객체를 두면서 느슨한 연결구조를 설계한다.**

#### **옵저버 (Observer) 패턴을 사용되는 경우**

-   **pub-sub (발행\[publish\] - 구독\[subscribe\]) 이벤트 기반 프로그래밍에 주로 사용되는 패턴**
-   **분산 이벤트 핸들링 시스템에서도 자주 사용된다.**
-   **옵저버(Observer) 패턴은 MVC 패러다임과 자주 결합되어, Model과 View 사이를 느슨하게 연결하기 위해 주로 사용된다고 한다.**

#### **구조**

-   **Subject : 여러 Observer들을 등록, 해지 기능 제공**
-   **1\. 클라이언트는 Subject에 Observer 등록, Subject 상태변경**
-   **2\. Subject는 상태가 변경되면 자신에게 등록된 모든 Observer를 순회하면서 Observer가 제공하는 메소드 호출**
-   **Observer : Observer가 해야 할 일, 규약**
-   **ConcreteObserver : Observer 구현체**

![](/assets/img/posts/247/1.png)

* * *

#### **옵저버 (Observer) 패턴 적용 전**

-   **간단한, 작은 단체 채팅방을 예시**

```java
@Getter
@AllArgsConstructor
public class UserBefore {
    private ChatServerBefore chatServer;

    public void sendMessage(String subject, String message) {
        chatServer.add(subject, message);
    }

    public List<String> getMessage(String subject) {
        return chatServer.getMessage(subject);
    }
}
```

```java
public class ChatServerBefore {

    private Map<String, List<String>> messages;

    public ChatServerBefore() {
        this.messages = new HashMap<>();
    }

    public void add(String subject, String message) {
        if(messages.containsKey(subject)) {
            messages.get(subject).add(message);
        }else {
            List<String> messageList = new ArrayList<>();
            messageList.add(message);
            messages.put(subject, messageList);
        }
    }

    public List<String> getMessage(String subject) {
        return messages.get(subject);
    }
}
```

```java
    @Test
    void test1() {
        ChatServerBefore chatServer = new ChatServerBefore();
        
        UserBefore user1 = new UserBefore(chatServer);
        user1.sendMessage("테스트", "안녕안녕");
        user1.sendMessage("리얼", "화이팅!");

        // 주체가 주기적으로 요청해서 값을 가져온다
        UserBefore user2 = new UserBefore(chatServer);
        System.out.println(user2.getMessage("테스트"));

        // 주체가 주기적으로 요청해서 값을 가져온다
        user1.sendMessage("테스트", "반갑다");
        System.out.println(user2.getMessage("테스트"));
    }
```

   
**클라이언트 코드를 보면, 주체가 주기적으로 값을 요청해서 가져오는 것 을 확인할 수 있다.**

* * *

#### **옵저버 (Observer) 패턴 적용 후**

-   **구조는 Subject 역할을 하는 ChatServer가 존재 ( Subject -> ChatServer)**
-   **ChatServer의 register 즉, 단체톡방을 Observer로 (Observer -> Subcriber)**
-   **단체 톡방에 종속된 객체인 ConcreteObserver를 User 클래스를 구현하겠다. (ConcreteObserver -> User)**

**√ ChatServer 이벤트 발생 -> 등록된 Observer가 관찰 -> ConcreteObserver들의 행위 실행이 된다.**  
 

-   **Observer 클래스**
-   **Subscriber라는 인터페이스로 정의, notify 메소드를 정의한다.**

```java
public interface Subscriber {
    void notifyHandleMessage(String message);
}
```

-   **Observer를 상속받아 각각의 행위를 캡슐화하여 구현한 ConcreteObserver 클래스**
-   **User라는 객체 생성**
-   **이벤트가 발생했을 때, 실행할 notify를 정의**

```java
@Getter
@AllArgsConstructor
public class User implements Subscriber{
    private String name;
    @Override
    public void notifyHandleMessage(String message) {
        System.out.println("받는사람 (" + name + ") " + message);
    }
}
```

-   **Subject 클래스로 채팅서버를 둔다**
-   **Subject 클래스에서 이벤트 등록, 해지, 발생의 책임을 담당**

```java
public class ChatServer {
    private Map<String, List<Subscriber>> subscribers = new HashMap<>();

    //등록 - Observer 등록
    public void register(String group, Subscriber subscriber) {
        if(subscribers.containsKey(group)) {
            subscribers.get(group).add(subscriber);
            return;
        }
        List<Subscriber> list = new ArrayList<>();
        list.add(subscriber);
        this.subscribers.put(group, list);
    }

    // 햐지
    public void unregister(String subject, Subscriber subscriber) {
        if(subscribers.containsKey(subject)) {
            subscribers.get(subject).remove(subscriber);
        }
    }

    // 이벤트 발생
    public void sendMessage(User user, String group, String message) {
        String userMessage = "보내는사람 (" + user.getName() + ") : " + message;

        // observer list에 등록된 각각의 observer들의 notify 실행
        if(subscribers.containsKey(group)) {
            System.out.println("======= 단톡방 : " + group + " =======");
            this.subscribers.get(group).forEach(s -> s.notifyHandleMessage(userMessage));
        }
    }
}
```

   
 

-   **각각의 유저를 정의하고, 해당 유저를 ChatServer의 Observer(채팅방)에 등록**
-   **ChatServer(Subject)의 특정한 이벤트 (SendMessage)가 발행될 때, 이를 바라보고 있던 Observer 클래스들의**  
    **notify가 실행된다.**

```java
    @Test
    void test2() {
        ChatServer chatServer = new ChatServer();
        User user1 = new User("A");
        User user2 = new User("B");

        chatServer.register("테스트", user1);
        chatServer.register("테스트", user2);

        chatServer.register("리얼", user1);

        chatServer.sendMessage(user1, "테스트", "안녕안녕");

        System.out.println();

        chatServer.sendMessage(user2, "리얼", "재밌구만~~~" );
    }
```

```java
======= 단톡방 : 테스트 =======
받는사람 (A) 보내는사람 (A) : 안녕안녕
받는사람 (B) 보내는사람 (A) : 안녕안녕

======= 단톡방 : 리얼 =======
받는사람 (A) 보내는사람 (B) : 재밌구만~~~
```

* * *

#### **옵저버(Observer) 패턴**

#### **장점**

-   **상태를 변경하는 객체와 변경을 감지하는 객체의 의존성을 느슨하게 유지할 수 있다.**
-   **Subject의 상태변경을 주기적으로 조회하지 않아도 자동적으로 감지할 수 있다.**
-   **발행자(Subject) 코드를 변경하지 않고도 새 구독자(Observer) 클래스를 도입할 수 있어서 OCP 준수**
-   **런타임시에 Observer를 추가하거나 제거할 수 있다.**

#### **단점**

-   **복잡도가 증가**
-   **패턴을 잘못 구현할 경우, 데이터 배분에 문제가 발생하여 위험도가 크다**
-   **다수의 Observer 객체를 등록 이후 해지하지 않는다면 Memory Leak가 발생할 수 있다.**  
    **위 예제에서, Map에 담겨있는 Observer가 명시적으로 해제되지 않고, Reference를 가지고 있다면**  
    **가비지 컬렉션의 대상이 되지 않는다.**  
    **즉, 이벤트가 계속 쌓이고, 메모리 관리가 되지 않는다면 큰 문제가 된다.**
