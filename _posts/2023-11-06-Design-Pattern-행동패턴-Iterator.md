---
title: "[Design Pattern] 행동패턴 - Iterator"
date: 2023-11-06 14:09:29 +0900
categories: ["Language", "Design Pattern"]
tags: ["java iterator", "java 반복자패턴", "디자인패턴", "디자인패턴 iterator", "자바 iterator", "자바 이터레이터"]
tistory_url: "https://aroma-bok.tistory.com/entry/Design-Pattern-%ED%96%89%EB%8F%99%ED%8C%A8%ED%84%B4-Iterator"
---
### **반복자 (Iterator) 패턴**

-   **집합 객체 내부 구조를 노출시키지 않고 순회하는 방법을 제공하는 패턴**
-   **반복자 패턴의 본질은 기반이 되는 표현을 노출시키지 않고 연속적으로 객체 요소에 접근하는 방법을 제공하는 것**
-   **즉, aggregate (집합체) 유형에 무관한 동일 순차 접근 방법을 제공하는 것이다**
-   **aggregate란 반복자 객체를 생성하기 위한 인터페이스를 정의하는 것이고, iterator란 요소에 접근할 수 있고 순회할 수 있는 인터페이스를 정의하는 것**
-   **이러한 반복자 패턴을 사용하면 집합 객체를 순회하는 클라이언트 코드를 변경하지 않고 다양한 순회 방법을 제공할 수 있다.**

#### **구조**

-   **Iterator : 집합체의 요소들을 순서대로 검색하기 위한 인터페이스 정의**
-   **ConcreateIterator - Iterator 인터페이스를 구현함**
-   **Aggregate - 여러 요소들로 이루어져 있는 집합체, Iterator의 역할을 만드는 인터페이스를 정한다.**
-   **ConcreateAggregate - Aggregate 인터페이스를 구현하는 클래스**

![](/assets/img/posts/244/1.png)

* * *

#### **반복자 (Iterator) 패턴 적용 전**

-   **다음은 게시글(Post)과 게시판(Board)을 표현한 인스턴스이다.**
-   **게시글에는 제목 title과 게시글 발행 날짜 필드가 있다.**

```java
@Getter
@AllArgsConstructor
public class Post {
    private String title;
    private LocalDate createdDate;
}
```

```java
@Getter
public class Board {
    private final List<Post> posts = new ArrayList<>();

    public void addPost(String title, LocalDate date) {
        posts.add(new Post(title, date));
    }
}
```

-   **일반적으로 for문을 돌려 집합체의 요소들을 순회한다.**
-   **이러한 구성 방식은 Board에 들어간 Post를 순회할 때, Board가 어떠한 구조로 이루어져 있는지를 클라이언트에 노출**

```java
@Test
    void test1() {
        Board board = new Board();
        board.addPost("디자인 패턴", LocalDate.of(2023,11,04));
        board.addPost("디자인 패턴을 공부해요",LocalDate.of(2023,11,03));
        board.addPost("디자인 패턴을 공부시러요",LocalDate.of(2023,11,01));

        // TODO 등록된 순서로 순회하기 -> 클라이언트가 게시판이 list의 구조라는 것을 알고 있어야 하는 순회
        // -> list 타입이 아닌 다른 무엇인가로 변경된다면 클라이언트 코드도 수정되어야 함
        List<Post> posts = board.getPosts();
        for(int i=0; i<posts.size(); i++) {
            Post post = posts.get(i);
            System.out.println(post.getTitle() + " " + post.getCreatedDate() + "");
        }

        System.out.println();

        //TODO 가장 최신 글 먼저 순회하기
        Collections.sort(posts, (p1, p2) -> p1.getCreatedDate().compareTo(p2.getCreatedDate()));
        for(int i=0; i<posts.size(); i++) {
            Post post = posts.get(i);
            System.out.println(post.getTitle() + " " + post.getCreatedDate());
        }
    }
```

```java
디자인 패턴 2023-11-04
디자인 패턴을 공부해요 2023-11-03
디자인 패턴을 공부시러요 2023-11-01

디자인 패턴을 공부시러요 2023-11-01
디자인 패턴을 공부해요 2023-11-03
디자인 패턴 2023-11-04
```

* * *

#### **반복자 (Iterator) 패턴 적용 후**

-   **자바에서는 이미 이터레이터 (Iterator) 인터페이스를 지원한다.**
-   **자바의 내부 이터레이터를 재활용해서 메서드 위임을 통해 코드를 간단하게 구현할 수 있다.**  
      
    

-   **순회 전략으로는 리스트 저장 순서대로 조회와 날짜 순서대로 조회 두 가지가 존재한다.**  
    **따라서 이에 대한 이터레이터 클래스 역시 두 가지 생성해 주면 된다.**  
    **1\. ListPostIterator  : 저장 순서 이터레이터**  
    **2\. DatePostIterator : 날짜 순서 이터레이터**

```java
public class ListPostIterator implements Iterator<Post> {
    private Iterator<Post> itr;

    public ListPostIterator(List<Post> posts) {
        this.itr = posts.iterator();
    }

    @Override
    public boolean hasNext() {
        return this.itr.hasNext(); // 자바 내부 이터레이터에 위임
    }

    @Override
    public Post next() {
        return this.itr.next(); // 자바 내부 이터레이터에 위임
    }
}
```

```java
public class DatePostIterator implements Iterator<Post> {
    private Iterator<Post> itr;

    public DatePostIterator(List<Post> posts) {
        // 최신 글 목록이 먼저 오도록 정렬
        Collections.sort(posts, (p1,p2) -> p1.getCreatedDate().compareTo(p2.getCreatedDate()));
        this.itr = posts.iterator();
    }

    @Override
    public boolean hasNext() {
        return this.itr.hasNext(); // 자바 내부 이터레이터에 위임
    }

    @Override
    public Post next() {
        return this.itr.next(); // 자바 내부 이터레이터에 위임
    }
}
```

-   **ListPostIterator와 DatePostIterator 객체를 반환하는 팩토리 메소드를 Boad 클래스에 추가만 해주면 완성**

```java
@Getter
public class Board {
    private final List<Post> posts = new ArrayList<>();

    public void addPost(String title, LocalDate date) {
        posts.add(new Post(title, date));
    }

    public List<Post> getPosts() {
        return posts;
    }

    // ListPostIterator 이터레이터 객체 반환
    public Iterator<Post> getListPostIterator() {
        return new ListPostIterator(posts);
    }

    // DatePostIterator 이터레이터 객체 반환
    public Iterator<Post> getDatePostIterator() {
        return new DatePostIterator(posts);
    }
}
```

-   **이제 클라이언트는 게시글을 순회할 때, Board 내부가 어떤 집합체로 구현 (Array, List, Tree, Queue... 등)**  
    **되어있는지 알 수 없게 감추고 전혀 신경 쓸 필요가 없게 되었다.**
-   **순회 전략을 각 객체로 나눔으로써 때에 따라 적절한 이터레이터 객체만 받으면 똑같은 이터레이터 순회 코드로 다양한 순회 전략을 구사할 수 있다.**

```java
    @Test
    void test2() {
        //게시판 생성
        Board board = new Board();

        // 게시판 글 포스팅
        board.addPost("디자인 패턴?", LocalDate.of(2023, 11, 03));
        board.addPost("디자인 패턴은 어렵다", LocalDate.of(2023, 11, 01));
        board.addPost("디자인 패턴은 쉽다", LocalDate.of(2023, 11, 06));
        board.addPost("디자인 패턴 공부는 재밌다", LocalDate.of(2023, 10, 01));

        // 게시글 발행 순서대로 조회
        Iterator<Post> listPostIterator = board.getListPostIterator();
        while (listPostIterator.hasNext()) {
            System.out.println(listPostIterator.next().getTitle());
        }

        // 게시글 날짜별로 조회
        Iterator<Post> datePostIterator = board.getDatePostIterator();
        while (datePostIterator.hasNext()) {
            System.out.println(datePostIterator.next().getTitle());
        }
    }
```

* * *

#### **Iterator 패턴을 사용하는 추가 이유**

-   **집합체 구현과 분리를 할 수 있다**
-   **Iterator 객체를 반환하면 컬렉션을 순회할 때, hasNext()와 next()라는 Iterator의 메소드만을 이용하기 때문에**  
    **집합체인 Board의 내부 구성을 감출 수 있게 된다. 즉, while문은 Board의 구현에 의존하지 않는다.**
-   **결과적으로, Board 집합체를 수정하더라도 클라이언트 코드를 수정하지 않아도 된다는 말이다.**

```java
        Iterator<Post> listPostIterator = board.getListPostIterator();
        while (listPostIterator.hasNext()) {
            System.out.println(listPostIterator.next().getTitle());
        }
```

* * *

#### **Iterator 패턴**

**장점**

-   **집합체 클래스의 응집도를 높여준다**
-   **집합체 내에서 일이 처리되는 방식을 알 필요 없이, 집합체 안에 들어있는 모든 항목에 접근할 수 있게 해 준다.**
-   **모든 항목에 접근하는 작업을 컬렉션 객체가 아닌 Iterator 객체가 맡게 된다. 이렇게 되면 집합체의 인터페이스 및  
    구현이 간단해진다.**
-   **그리고 집합체에서는 반복 작업에서 손을 떼고 원래 자신이 할 일에만 전념할 수 있다.**

**단점**

-   **단순한 순회를 구현하는 경우에는 클래스만 많아져서 구조 복잡도가 증가할 수 있다.**
