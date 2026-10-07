---
title: "[JPA] JPA란? 그리고 JPA에서 자주 사용되는 어노테이션"
date: 2025-01-13 15:38:23 +0900
categories: ["Framework & Library", "JPA"]
tags: ["jpa", "jpa 개념", "jpa 어노테이션", "jpa 영속성", "jpa 영속성 컨텍스트", "jpa 예시", "jpa 캐시", "jpa 테이블 매핑", "jpa란", "영속성 컨텍스트"]
tistory_url: "https://aroma-bok.tistory.com/entry/JPA-JPA%EB%9E%80-%EA%B7%B8%EB%A6%AC%EA%B3%A0-JPA%EC%97%90%EC%84%9C-%EC%9E%90%EC%A3%BC-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-%EC%96%B4%EB%85%B8%ED%85%8C%EC%9D%B4%EC%85%98"
---
![](/assets/img/posts/301/1.png)

#### **JPA( Java-Persistence API)**

-   **Java 애플리케이션에서 데이터베이스와 객체 간 매핑(ORM)을 처리하는 표준 API**
-   **JPA는 복잡한 검색 조건을 사용해서 엔티티 객체를 조회할 수 있는 다양한 쿼리기술을 지원**
-   **데이터베이스에 의존적인 SQL을 작성하는 대신 객체를 사용해 데이터베이스 작업을 수행할 수 있도록 지원**

#### **JPA 주요 개념**

1.  **엔티티(Entity)**  
    **데이터베이스 테이블과 매핑되는 자바 클래스**  
    **각 엔티티는 @Entity 어노테이션으로 표시**  
    **엔티티의 필드는 데이터베이스의 컬럼에 매핑**
2.  **영속성(Persistence)**  
    **애플리케이션에서 객체를 영구적으로 저장, 읽기, 수정, 삭제 등의 작업을 수행**  
    **영속성 컨텍스트는 JPA에서 이러한 영속성을 관리하는 핵심 역할**
3.  **EntityManager**  
    **엔티티를 저장하고, 수정하고, 삭제하고, 조회하는 작업을 담당**  
    **영속성 컨텍스트를 직접 관리**

* * *

#### **영속성 컨텍스트(Persistence Context)**

-   **영속성 컨텍스트는 엔티티 객체를 저장(영속)하고 관리하는 JPA의 저장소**
-   **데이터베이스와 애플리케이션 간의 1차 캐시 역할**

#### **영속성 컨텍스트의 주요 특징**

1.  **엔티티 생명주기 관리**
    
    ```java
    Transient(비영속) : 영속성 컨텍스트와 관련이 없는 상태
    User user = new User(); // 비영속 상태
    
    Persistence(영속) : 영속성 컨텍스트에 저장된 상태
    entityManger.persist(user); // 영속 상태
    
    Detached(준영속) : 영속성 컨텍스트에서 분리된 상태
    entityManger.detach(user); // 준영속 상태
    
    Removed(삭제) : 삭제 요청 상태
    entityManger.remove(user); // 삭제 상태
    ```
    
2.  **1차 캐시**  
    **영속성 컨텍스트는 1차 캐시를 사용해 데이터베이스 조회 전에 이미 메모리에 있는 엔티티를 우선 반환**  
    **동일한 트랜잭션 내에서 동일한 ID로 조회하면 항상 같은 객체를 반환**
3.  **변경 감지(Dirty Checking)**  
    **영속성 컨텍스트는 엔티티의 상태를 감지하고, 변경이 있을 경우 트랜잭션 종료 시점에 자동으로 데이터베이스에 반영**  
    
    ```java
    User user = entityManger.find(User.class, 1L);
    user.setName("newName"); // 변경 감지
    ```
    
4.  **쓰기 지연 (Write Behind)**  
    **변경된 데이터를 즉시 데이터베이스에 반영하지 않고, 트랜잭션 종료 시점에 SQL을 일괄 실행**  
    **Insert/Update/Delete 작업이 하나의 SQL Batch로 처리**
5.  **동일성 보장 (Identity Guarantee)**  
    **동일한 영속성 컨텍스트 내에서 동일한 엔티티 ID를 가진 객체는 항상 같은 객체로 보장**  
    
    ```java
    User user1 = entityManger.find(User.class, 1L);
    User user2 = entityManger.find(User.class, 1L);
    System.out.println(user1 == user2); // true
    ```
    

* * *

#### **영속성 컨텍스트의 장점**

1.  **성능 최적화**  
    **1차 캐시와 쓰기 지연을 통해 데이터베이스 접근을 최소화하고 성능을 향상**
2.  **변경감지**  
    **애플리케이션 코드에서 명시적으로 SQL을 작성하지 않아도 변경 사항을 자동으로 감지해 처리**
3.  **동일성 보장**  
    **동일 트랜잭션에서 동일 동일한 ID의 엔티티는 동일한 객체로 관리**
4.  **트랜잭션 관리**  
    **데이터베이스 작업이 하나의 트랜잭션 내에서 일관성을 유지할 수 있도록 관리**

* * *

#### **JPA에서 자주 사용되는 어노테이션**

#### **1\. 엔티티 클래스 관련 어노테이션**

-   **@Entity**  
    **클래스가 JPA 엔티티임을 선언**  
    **데이터베이스의 테이블과 매핑**  
    
    ```java
    @Entity
    public class User {
        @Id
        @GeneratedValue
        private Long id;
    }
    ```
    
-   **@Table**  
    **엔티티와 매핑될 데이터베이스 테이블을 지정**  
    **name 속성을 통해 테이블 이름 지정 가능**  
    
    ```java
    @Entity
    @Table(name = "users")
    public class User {
        @Id
        @GeneratedValue
        private Long id;
    }
    ```
    

#### **2\. 필드 매핑 관련 어노테이션**

-   **@Id**  
    **엔티티의 기본 키(PK)를 지정**
-   **@GeneratedValue**  
    **기본 키의 자동 생성 전략 strategy : GenerationType.IDENTITY, GenerationType.SEQUENCE, GenerationType.TABLE, GenerationType.AUTO**  
    
    ```java
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    ```
    
-   **@Column**  
    **필드를 데이터베이스 컬럼과 매핑**  
    **name, nullable, unique, length, updatable 등의 속성 제공**  
    
    ```java
    @Column(name = "user_name", nullable = false, unique = true)
    private String name;
    ```
    
-   **@Enumerated**  
    **열거형(Enum)을 매핑**  
    **EnumType.STRING(문자열), EnumType.ORDINAL(숫자)로 설정 가능**  
    
    ```java
    @Enumerated(EnumType.STRING)
    private UserType userType;
    ```
    
-   **@Lob**  
    **CLOB(문자 대용량 데이터) 또는 BLOB(이진 대용량 데이터)를 매핑**  
    
    ```java
    @Lob
    private String description;
    ```
    
-   **@Transient**  
    **데이터베이스와 매핑되지 않는 필드 지정**  
    
    ```java
    @Transient
    private String tempData;
    ```
    

#### **3\. 관계 매핑 관련 어노테이션**

-   **@OneToOne**  
    **1:1 관계 매핑**  
    
    ```java
    @OneToOne
    @JoinColumn(name = "profile_id")
    private Profile profile;
    ```
    
-   **@OntToMany**  
    **1:N 관계 매핑**  
    
    ```java
    @OneToMany(mappedBy = "user")
    private List<Order> orders;
    ```
    
-   **@ManyToOne**  
    **N:1 관계 매핑**  
    
    ```java
    @ManyToOne
    @JoinColumn(name = "team_id")
    private Team team;
    ```
    
-   **@ManyToMany**  
    **N:N 관계 매핑**  
    
    ```java
    @ManyToMany
    @JoinTable(
        name = "user_role",
        joinColumns = @JoinColumn(name = "user_id"),
        inverseJoinColumns = @JoinColumn(name = "role_id")
    )
    private List<Role> roles;
    ```
    
-   **@JoinColumn**  
    **외래키를 매핑**
-   **@JoinTable**  
    **다대다 관계에서 연결 테이블 지정**

* * *

**JPA의 영속성 컨텍스트는 데이터베이스와 객체 간의 중간 역할을 하며, 개발자가 SQL 작성 없이 객체 중심의**

**프로그래밍을 할 수 있도록 지원한다. 이를 통해 생산성을 높이고 유지보수를 용이하게 한다.**

**하지만, 성능 최적화와 복잡한 쿼리 작업에서는 한계가 있을 수 있기에 필요에 따라 다른 기술과 병행 사용하는 것이 좋다.**
