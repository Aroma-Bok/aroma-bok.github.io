---
title: "[Design Pattern] 구조패턴 - Facade"
date: 2023-10-30 08:05:05 +0900
categories: ["Language", "Design Pattern"]
tags: ["design pattern facade", "design pattern 퍼사드 패턴", "디자인패턴 facade", "디자인패턴 facade 백기선"]
tistory_url: "https://aroma-bok.tistory.com/entry/Design-Pattern-%EA%B5%AC%EC%A1%B0%ED%8C%A8%ED%84%B4-Facade"
---
### **퍼사드 (Facade) 패턴**

-   **복잡한 서브 시스템의 의존성을 최소화하기 위한 목적을 가지는 패턴**
-   **클라이언트가 사용해야 하는 복잡한 서브 시스템의 의존성을 간단한 인터페이스로 추상화할 수 있다고 한다.**
-   **어떤 서브시스템의 일련의 인터페이스에 대한 통합된 인터페이스를 제공**
-   **퍼사드 패턴의 궁극적인 목표는 '객체 간의 의존성을 느슨하게 만드는 것'이다**

* * *

#### **퍼사드 (Facade) 패턴을 사용하기 좋은 시기**

-   **시스템이 너무 복잡할 때**
-   **간단한 인터페이스를 통해 복잡한 시스템을 접근하도록 하고 싶을 때**
-   **시스템을 사용하고 있는 외부와 결합도가 너무 높을 때, 의존성을 낮추기 위할 때**

   
 

-   **Facade : 서브시스템 기능을 편리하게 사용할 수 있도록 하기 위해 여러 시스템과 상호 작용하는 복잡한 로직을 재정리해서 높은 레벨의 인터페이스를 구성한다. Facade 역할은 서브 시스템의 많은 역할게 대해 '창구'가 된다.**
-   **클라이언트와 서브시스템이 서로 긴밀하게 연결되지 않도록 한다.**
-   **Additional Facade : 퍼사드 클래스는 반드시 한 개만 존재해야 한다는 규칙 같은 건 없다. 연관되지 않은 기능이 있다면 얼마든지 Facade 2세로 분리한다. 이 Facade 2세는 다른 Facade에서 사용할 수도 있고 클라이언트에서 직접 접근할 수 도 있다.**
-   **SubSystem : 여러 개의 라이브러리 혹은 클래스들**
-   **Client : 서브 시스템에 직접 접근하는 대신 Facade를 사용**

![](/assets/img/posts/240/1.png)

* * *

#### **퍼사드 (Facade) 패턴 적용 전**

-   **DB로부터 값을 얻어와 화면에 데이터를 출력하려는 프로그램이 있다고 가정**
-   **패키지에는 총 4개, Cache, DBMS, Row, Message 클래스가 존재**

#### **작업순서**

1.  **DBMS를 조회하기 전에 저장된 Cache를 먼저 확인**
2.  **Cache에 데이터가 없으면 DBMS 통해서 조회**
3.  **Cache에 데이터가 있으면 가공하고 출력**
4.  **조회된 데이터를 가공하고 출력하고 Cache에 저장**

```java
@AllArgsConstructor
@NoArgsConstructor
public class DBMS {
    // DB역할을 하는 클래스

    private HashMap<String, Row> db = new HashMap<>();

    public void put(String name, Row row) {
        db.put(name, row);
    }

    public Row query(String name) {
        try {
            Thread.sleep(500); // 0.5초 대기 구현
        }catch (InterruptedException e) {
        }
        return db.get(name);
    }
}
```

```java
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class Row {
    // DBMS에 저장된 데이터 클래스

    private String name;
    private String age;
    private String email;
}
```

```java
public class Cache {
    //DB에서 조회된 데이터를 임시 저장해주는 Cache
    private HashMap<String, Row> cache = new HashMap<>();

    public void put(Row row) {
        cache.put(row.getName(), row);
    }

    public Row get(String name) {
        return cache.get(name);
    }
}
```

```java
@NoArgsConstructor
@AllArgsConstructor
public class Message {
    // Row클래스를 출력하는 클래스

    private Row row;

    public String nmakeName() {
        return "Name : \"" + row.getName() + "\"";
    }

    public String makeAge() {
        return "Age : \"" + row.getAge() + "\"";
    }

    public String makeEmail() {
        return "Email : \"" + row.getEmail() + "\"";
    }
}
```

-   **패키지의 코드를 그대로 메인 로직에 작성하여 구현하고 있다. 데이터 조회부터 출력까지 여러 개의 객체가 사용된다.**
-   **수정이나 확장에 있어 개발자가 실수를 할 수 있는 가능성이 많다.**

```java
@Test
    void test1() {
        // DB 생성 등록
        DBMS dbms = new DBMS();
        dbms.put("A", new Row("A", "010-1234-5678", "A@nver.com"));
        dbms.put("B", new Row("B", "010-2345-5678", "B@nver.com"));
        dbms.put("C", new Row("C", "010-3678-5678", "C@nver.com"));

        //Cache생성
        Cache cache = new Cache();

        // DB를 조회하기 전, Cache 먼저 확인
        String name = "A";
        Row row = cache.get(name);

        // Cache 데이터 존재유무 확인
        if(row == null) {
            row = dbms.query(name);
            if(row != null) { // 조회된 데이터가 있으면 Cache에 저장
                cache.put(row);
            }
        }

        // dbms에 조회된 데이터 확인
        if(row != null) {
            Message message = new Message(row);

            System.out.println(message.nmakeName());
            System.out.println(message.makeAge());
            System.out.println(message.makeEmail());
        }else {
            System.out.println(name +  " 가 DB에 존재하지 않음.");
        }
    }
```

* * *

#### **퍼사드 (Facade) 패턴 적용 후**

-   **퍼사드 (Facade) 패턴은 다른 디자인 패턴과는 다르게 클래스 구조가 정형화되지 않은 패턴이다.**
-   **반드시 클래스 위치가 어떻고 어떤 형식으로 위임을 해야 된다는 것이 없다.**
-   **퍼사드 (Facade) 클래스를 만들어 적절히 기능 집약화만 해주면 그게 디자인 패턴이 된다.**

-   **단순화된 인터페이스를 통해 서브 클래스를 다룸으로써 개발자의 실수를 줄이고자 하는 것이 퍼사드 (Facade) 패턴**

```java
public class Facade {
    private DBMS dbms = new DBMS();
    private Cache cache = new Cache();

    public void insert() {
        dbms.put("A", new Row("A", "010-1234-5678", "A@nver.com"));
        dbms.put("B", new Row("B", "010-2345-5678", "B@nver.com"));
        dbms.put("C", new Row("C", "010-3678-5678", "C@nver.com"));
    }

    public void run(String name) {
        Row row = cache.get(name);

        // 캐시에서 확인
        if(row == null) {
            row = dbms.query(name); // DB에 해당 데이터를 조회해서 row에 저장
            if(row != null) { // 조호된 데이터가 있다면 캐시에 저장
                cache.put(row);
            }
        }

        // 조회된 row 값이 있으면 출력
        if(row != null) {
            Message message = new Message(row);

            System.out.println(message.nmakeName());
            System.out.println(message.makeAge());
            System.out.println(message.makeEmail());
        }else { // 조호된 내용이 없을 때
            System.out.println(name + " 가 DB에 존재하지 않습니다.");
        }
    }
```

-   **퍼사드 (Facade) 패턴을 적용하니 로직이 훨씬 간결해졌다.**
-   **퍼사드 (Facade) 패턴의 핵심은 인터페이스(Api)를 적게 구성하는 것**
-   **라이브러리에서 제공하는 클래스나 메소드가 많이 보이면, 프로그래머는 무엇을 사용하면 좋을지 생각하게 되고  
    순서도 doc을 살펴보며 확인해야 한다.**

```java
    @Test
    void test2() {
        // 퍼사드 (Facade) 객체 생성
        Facade facade = new Facade();

        // db에 insert
        facade.insert();

        // 퍼삳드를 통해 캐시, DB 조회 그리고 출력을 한번에 실행
        String name = "A";
        facade.run(name);
    }
```

   
 

> **퍼사드(Facade)는 서브 클래스들의 캡슐화를 하는 것이 아니다.  
> 서브 클래스들을 사용할 간단한 인터페이스를 제공할 뿐이다.  
> 사용자가 서브 시스템 내부의 클래스를 직접 사용하는 것을 제한할 수는 없다.**  
> **그래서 추상화에 가깝다고 볼 수 있다.  
> **  
> ***※ 보통 퍼사드 (Facade) 객체는 하나만 있어도 충분하므로 싱글톤으로 구성해 주면 좋다.***

* * *

#### **퍼사드 (Facade) 패턴**

#### **장점**

-   **서브 시스템의 복잡성에서 코드를 분리하여, 외부에서 시스템을 사용하기 쉬워진다.**
-   **서브 시스템 간의 의존 관계가 많을 경우 이를 감소시키고 의존성을 한 곳으로 모을 수 있다.**
-   **복잡한 코드를 숨기고, 클라이언트가 시스템의 코드를 몰라도 Facade 클래스만 이해하면 사용 가능하다.**

#### **단점**

-   **Facade가 앱의 모든 클래스에 결합된 God 객체가 될 수 있다.**
-   **Facade 클래스 자체가 서브 시스템에 대한 의존성을 가지게 되어 의존성을 완전히 피할 순 없다.**
-   **추가적인 코드가 생겨나기에 유지보수 측면에서 공수가 발생**
-   **추상화하고자 하는 시스템이 얼마나 복잡한지 Facade 패턴을 통해서 얻게 되는 이점과 추가적인**  
    **유지보수 비용을 비교해 보면서 결정 필요.**
