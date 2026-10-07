---
title: "[Design Pattern] 구조 패턴 - Decorator"
date: 2023-10-27 10:05:54 +0900
categories: ["Language", "Design Pattern"]
tags: ["decorator", "design pattern decorator", "데코레이터 패턴", "디자인 패턴 decorator", "디자인패턴 decorator", "디자인패턴 decorator 백기선"]
tistory_url: "https://aroma-bok.tistory.com/entry/Design-Pattern-%EA%B5%AC%EC%A1%B0-%ED%8C%A8%ED%84%B4-Decorator"
---
### **Decorator  패턴**

-   **기존 코드를 변경하지 않고 부가기능을 추가하는 패턴**
-   **객체의 결합을 통해 기능을 동적으로 유연하게 확장할 수 있게 해주는 패턴**
-   **동적으로 유연하다는 것은 '런타임 시에 부가기능을 추가'가 가능하다는 것**
-   **결합이란 상속이 아닌 위임 (혹은 합성)  즉, Has-a 관계를 가지는 패턴임을 의미**
-   **Decorator  객체를 조합함으로써 추가 기능의 조합을 설계하는 패턴**

-   **Composite 패턴처럼 최상위에 Component 역할을 하는 인터페이스가 존재한다.**
-   **이 인터페이스는 ConcreateComponent와 Decorator가 둘 다 구현을 한 인터페이스다.**
-   **Composite 패턴과 유사하지만 차이점은 Decorator가 여러 개의 Decorator를 가지고 있는 게 아니라 하나의**
-   **Component  타입의 인스턴스를 가지고 있다.**
-   **Decorator는 가지고 있는 하나의 Component를 호출하며, 호출 전, 후 부가적인 일들을 할 수 있다.** 

![](/assets/img/posts/239/1.png)

  
 

* * *

#### **Decorator 패턴 적용 전**

-   **클라이언트는 CommentService의 addComment 기능을 통해 댓글 작성 가능**

```java
public class CommentService {
    public void addComment(String comment) {
        System.out.println(comment);
    }
}
```

```java
public class Client {
    private CommentService commentService;

    public Client(CommentService commentService) {
        this.commentService = commentService;
    }
    public void writeComment (String comment) {
        commentService.addComment(comment);
    }
}
```

-   **클라이언트에 CommentServcie를 주입하여 기능을 참조할 수 있도록 한다.**

```java
    @Test
    void test1() {
        Client client = new Client(new CommentService());
        client.writeComment("디자인패턴 공부");
        client.writeComment("디자인패턴 어렵다");
        client.writeComment("디자인패턴 그래도 공부해야지");
        client.writeComment("디자인패턴 잘하고싶다면 >>>> http://커몬커몬.com");
    }
```

   
   
**이제 이 기본적인 기능에서 부가적인 기능을 추가해 보자.**

* * *

#### **상속을 통한 기능 추가**

```java
public class TrimmingCommentService extends CommentService {
    @Override
    public void addComment(String comment) {
        super.addComment(trim(comment));
    }

    private String trim(String comment) {
        return comment.replace("!", "~"); // ! -> ~ 변경
    }
}
```

```java
public class SpamFilteringCommentService extends CommentService {
    @Override
    public void addComment(String comment) {
        if(!isNotSpam(comment)) { // http를 포함하면 댓글등록 X
            super.addComment(comment);
        }
    }

    private boolean isNotSpam(String comment) {
        return comment.contains("http");
    }
}
```

```java
    @Test
    void test2() {
        Client client = new Client(new CommentService());
        client.writeComment("디자인패턴 공부!!!");
        client.writeComment("디자인패턴 어렵다!!");
        client.writeComment("디자인패턴 그래도 공부해야지!!!!!!");
        client.writeComment("디자인패턴 잘하고싶다면 >>>> http://커몬커몬.com");

        Client trimClient = new Client(new TrimmingCommentService());
        trimClient.writeComment("디자인패턴 공부!!!");
        trimClient.writeComment("디자인패턴 어렵다!!");
        trimClient.writeComment("디자인패턴 그래도 공부해야지!!!!!!");
        trimClient.writeComment("디자인패턴 잘하고싶다면 >>>> http://커몬커몬.com");

        Client spamClient = new Client(new SpamFilteringCommentService());
        spamClient.writeComment("디자인패턴 공부!!!");
        spamClient.writeComment("디자인패턴 어렵다!!");
        spamClient.writeComment("디자인패턴 그래도 공부해야지!!!!!!");
        spamClient.writeComment("디자인패턴 잘하고싶다면 >>>> http://커몬커몬.com");
    }
```

#### **상속을 통한 기능의 확장 단점**

-   **가장 단순한 방법이지만, 만약 trim과 filter 기능을 동시에 사용하고 싶다면 새로운 클래스를 추가해서 추가적인 기능을 구현해야 한다. (*번거롭고 클래스가 많이 증가*한다.)**

* * *

#### **Decorator 패턴 사용**

-   **Decorator 객체를 조합함으로써 추가 기능의 조합을 설계하는 패턴을 사용해 보자.**
-   **위임 (혹은 합성)을 이용한 기능 확장**

-   **CommentService를 인터페이스로 만들고 기능과 구현을 분리한다.**
-   **CommentServiceForDecorator - Component이고 DefaultCommentService - ConcreateComponent이다**

```java
public interface CommentServiceForDecorator {
    void addComment(String comment);
}
```

```java
public class DefaultCommentService implements CommentServiceForDecorator{
    @Override
    public void addComment(String comment) {
        System.out.println(comment);
    }
}
```

```java
public class ClientForDecorator {
    private CommentServiceForDecorator commentServiceForDecorator;

    public ClientForDecorator(CommentServiceForDecorator commentServiceForDecorator) {
        this.commentServiceForDecorator = commentServiceForDecorator;
    }
    public void writeComment (String comment) {
        commentServiceForDecorator.addComment(comment);
    }
}
```

#### **1\. Decorator 패턴의 핵심**

-   **Decorator 역할을 하는 클래스가 딱 1개의 Component 만을 가지고 있으면서, 기능을 확장해 나가는 것 (Has-a 관계)**
-   **Component를 감싸고 있는 CommentDecorator를 생성한다. 그리고 Decorator 역시 Component 타입이다.**
-   **Decorator는 Component를 주입받고 기능만 호출해 준다.**

```java
public class CommentDecorator implements CommentServiceForDecorator{
    private CommentServiceForDecorator commentServiceForDecorator;
    
    public CommentDecorator(CommentServiceForDecorator commentServiceForDecorator) {
        this.commentServiceForDecorator = commentServiceForDecorator;
    }
    
    @Override
    public void addComment(String comment) {
        commentServiceForDecorator.addComment(comment);
    }
}
```

#### **2\. 기능의 확장**

-   **그 후에, 모든 기능의 확장은 Component 클래스를 감싸고 있는 Decorator (CommentDecorator)를 통한다.**
-   **여기서 중요한 것은, 확장된 기능을 Override 한 구현체에서 부모의 기능을 참조하여 기능을 확장한다는 것**
-   **그렇기 때문에 조합이 가능한 설계가 된다는 것이다.**

```java
public class TrimmingCommentDecorator extends CommentDecorator {
    public TrimmingCommentDecorator(CommentServiceForDecorator commentServiceForDecorator) {
        super(commentServiceForDecorator);
    }

    @Override
    public void addComment(String comment) {
        super.addComment(trim(comment));
    }

    public String trim(String comment) {
        return comment.replace("!", "~");
    }
}
```

```java
public class SpamFilteringCommentDecorator extends CommentDecorator {
    public SpamFilteringCommentDecorator(CommentServiceForDecorator commentServiceForDecorator) {
        super(commentServiceForDecorator);
    }

    @Override
    public void addComment(String comment) {
        if(!isNotSpam(comment)) {
            super.addComment(comment);
        }
    }

    private boolean isNotSpam(String comment) {
        return comment.contains("http");
    }
}
```

-   **결과적으로 기능이 결합되어 새로운 구현체를 만들지 않고도 trim 기능과 spam filter 기능을 조합할 수 있다.**

```java
    @Test
    void test3() {
        CommentServiceForDecorator commentService = new DefaultCommentService();

        if(spamYn) {
            commentService = new SpamFilteringCommentDecorator(commentService);
        }

        if(trimYn) {
            commentService = new TrimmingCommentDecorator(commentService);
        }

        ClientForDecorator client = new ClientForDecorator(commentService);
        client.writeComment("디자인패턴 공부!!!!!!");
        client.writeComment("디자인패턴 어렵다!!!!!!!");
        client.writeComment("디자인패턴 그래도 공부해야지");
        client.writeComment("디자인패턴 잘하고싶다면 >>>> http://커몬커몬.com");
    }
```

* * *

#### **실행시키는 순서**

***new TrimmingCommentDecorator(new SpamFilteringCommentDecorator(new DefaultCommentService()));***  
  
**이 순서로 생성자가 실행되면서 해당 메소드를 차례차례 실행시키는 원리이다.**  
   
**super로 최상위의 생성자나 메소드를 호출하고 있기에 결합하여 사용할 수 있다.**

* * *

* * *

#### **Decorator 패턴**

**장점** 

-   **기존 코드를 변경하지 않고 Decorator로 감싸기만 해서 부가기능을 추가할 수 있다.**
-   **새로운 클래스를 만들지 않고 기존 기능을 조합할 수 있고 런타임에 동적으로 기능을 변경할 수 있다.**

**단점**

-   **Decorator로 감싸면서 코드가 복잡해질 수 있는 단점이 있다.**
