---
title: "POJO vs EJB 차이점은?"
date: 2023-08-25 13:17:28 +0900
categories: ["Language", "Java"]
tags: ["pojo vs ejb", "pojo vs ejb 차이점"]
tistory_url: "https://aroma-bok.tistory.com/entry/POJO-vs-EJB-%EC%B0%A8%EC%9D%B4%EC%A0%90%EC%9D%80"
---
#### **POJO (Plain Old Java Object)**

**특정 프레임워크나 라이브러리에 의존하지 않는 순수한 자바 클래스**

**POJO는 간단한 데이터 객체를 나타내거나 비즈니스 로직을 포함할 수 있다.**

> **2000년 가을, 마틴 파울러는 EJB(Enterprise Java Bean)보다는 단순한 자바 오브젝트에 도메인 로직을 넣어**  
> **사용하는 것이 여러 가지 장점이 있는데도 사람들이 그 EJB가 아닌 '평범한 자바 오브젝트'를 사용하기를**  
> **꺼려하는지 의문을 가짐. 사람들이 EJB와 같은 명확한 이름이 없다고 생각하여 POJO라고 이름을 지음.**  
>   
> **토비 왈 "진정한 POJO란 객체지향적인 원리에 충실하면서, 환경과 기술에 종속되지 않고 필요에 따라 재활용될 수 있는 방식으로 설계된 오브젝트를 말한다."**  
> **\[단순히 기술규약과 환경에 종속되지 않는 게 POJO가 아니라는 것\] :자바의 순수 기능만을 사용하여 만든 클래스**

**특징**

-   **의존성이 없음 : 특정 프레임워크나 라이브러리에 종속되지 않고 Java의 기본 기능만으로 작성**
-   **Java Bean 규약을 따름 : Getter와 Setter 메서드를 사용하여 프로퍼티에 접근하고 수정하는 규약을 따름**
-   **가독성 및 유지보수성 : 간단하고 명확한 코드로 작성되므로 가독성이 높고 유지보수가 쉽다.**

```java
public class Student {
    private String name;
    private int age;

    // 기본 생성자
    public Student() {}

    // 매개변수가 있는 생성자
    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // Getter 및 Setter 메서드
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

* * *

#### **EJB (Enterprise JavaBeans)**

**엔터프라이즈 애플리케이션 개발을 위한 컴포넌트 모델**

**EJB는 Java EE(Java Platform, Enterprise Edition)의 일부로 제공되며, 서버 측의 비즈니스 로직을 처리하고**

**분산 환경에서의 트랜잭션, 보안등을 다루기 위해 사용.**

> **EJB를 사용하던 시절에는 객체지향적 설계의 장점을 포기하고 특정 기술과 환경을 사용하기에 급급했다고 한다.**

**종류**

-   **세션 빈 (Session Bean) : 클라이언트의 요청에 응답하는 로직을 담당.**  
    **\* '상태 없는 세션 빈'과 '상태 있는 세션 빈'으로 나눌 수 있음**
-   **엔티티 빈 (Entity Bean) : 데이터베이스의 레코드를 객체로 나타내는 컴포넌트.**
-   **메시지 빈 (Message Bean) : 비동기 메시지를 처리하기 위한 빈.**

```java
import javax.ejb.Stateless;

@Stateless
public class CalculatorBean implements CalculatorRemote {

    @Override
    public int add(int a, int b) {
        return a + b;
    }

    @Override
    public int subtract(int a, int b) {
        return a - b;
    }
}
```

* * *

**요약하자면, POJO는 순수한 자바 클래스로서 특정 제한이 없다.**

**반면에, EJB는 엔터프라이즈 애플리케이션의 구성요소로 사용되며** **분산 환경에서의 트랜잭션과 보안과 같은**

**고급 기능을 처리한다.**
