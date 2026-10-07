---
title: "JPA vs MyBatis 차이점?"
date: 2023-02-27 14:58:29 +0900
categories: ["Language", "Java"]
tags: ["jdbc", "jpa", "jpa vs mybatis 차이점", "mybatis", "rdb", "sql"]
tistory_url: "https://aroma-bok.tistory.com/entry/JPA-vs-MyBatis-%EC%B0%A8%EC%9D%B4%EC%A0%90"
---
**요즘 JPA를 사용하는 곳이 많아졌다. JPA에 관심을 갖게 되면서 기존에 MyBatis만 사용했지만 어떤 차이가 있는지 궁금해서 정리하게 되었다.**

* * *

**MyBatis를 정의하기 전에 JDBC는 알고 넘어가야 한다. 왜냐하면 MyBatis를 왜 사용하는가에 대해서 알고자 한다면,**

**JDBC는 필수적으로 나오는 개념이기 때문이다.**

#### **JDBC (Java DataBase Connectivity)**

-   **Java에서 DB에 접속할 수 있도록 도와주는 Java API**
-   **JDBC는 1개의 클래스 내에 반복되는 코드가 존재**
-   **한 파일에 Java와 SQL이 같이 사용되고 있어서 재사용과 가독성에 좋지 않음**

![](/assets/img/posts/186/1.png)

#### **MyBatis**

-   **JDBC의 작업을 간편하게 해주는 프레임워크**
-   **SQL문을 Java 코드에서 분리하여 XML 파일로 따로 관리**
-   **JDBC를 통해 수동으로 세팅한 파라미터와 결과의 매핑을 대신 JDBC로 처리하는 작업보다 더 간편하게 작업이 가능**
-   **JDBC에 비해 코드량이 적어 생산성을 높여줌**
-   **JDBC에서 사용해야 하는 Connection, Statement, Close 등을 MyBatis가 직접 관리해서 코드를 줄여줌**
-   **PreparedStatement와 같이 SQL Injection을 고려하지 않아도 됨**

> **Connection : DB와 연결된 상태를 표현하는 객체**  
>   
> **Statement : 객체 생성 이후 SQL문을 완성하여 DB에 바로바로 처리**  
> **(※ Connection 객체가 제공하는 createStatement() 메서드 사용**  
>   
> **PreparedStatement : 객체 생성 이후 '?'가 포함된 SQL문으로 생성하고 이후에 '?' 자리만 바꿔가며 처리**  
> **(※ Connection 객체가 제공하는 prepareStatement(sql) 메서드 사용 ('?'가 들어간 String 타입의 SQL문이 인자값)**

* * *

**그렇다면 JPA란 무엇일까? JPA를 알기 전에 ORM (Obejct Relational Mapping)이라는 개념을 알아야 한다.**

#### **ORM (Object Relational Mapping)**

-   **객체(Object)와 DB의 테이블을 Mapping 시켜 RDB 테이블을 객체지향적으로 사용하게 해주는 기술**
-   ****JPA (Java Persistence API)가 등장하기 이전에는 MyBatis라는 SQL Mapper 기술을 이용****

> **RDB (Relational DataBase)**  
> **: 관계형 데이터 모델에 기초를 둔 데이터베이스**  
>   
> **관계형 데이터 모델**  
> **: 데이터를 구성하는데 필요한 방법 중 하나로 모든 데이터를 2차원의 테이블 형태로 표현** 

#### **JPA**

-   **Java에서 ORM (Object Relational Mapping) 기술 표준으로 사용하는 인터페이스 모음**
-   **Java 애플리케이션에서 관계형 데이터베이스(RDB)를 사용하는 방식을 정의한 인터페이스**
-   **인터페이스이기 때문에 Hibernate, OpenJPA 등이 JPA를 구현함**

![](/assets/img/posts/186/2.png)

* * *

**MyBatis는 Java 클래스 코드와 SQL 코드를 Mapping 시켜서 사용하는 반면에, JPA와 같은 ORM 기술은 객체가 DB에 연결되기 때문에, SQL을 직접 작성하지 않고 표준 인터페이스 기반으로 처리한다는 점이 차이점이 있다.**

**JPA는 Java 클래스를 DB 테이블로 만들어주는 ORM 프레임워크**

**MyBatis는 SQL을 Java 메서드로 만들어주는 SQL Mapper**
