---
title: "[Spring Boot] Spring Data JPA 간단 CRUD 사용 및 데이터베이스 설정"
date: 2023-03-07 17:50:44 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["getone vs findbyid 차이점", "hibernate 사용", "jpa crud", "jpa 데이터 베이스 설정", "jparepository 메서드"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Boot-Spring-Data-JPA-%EA%B0%84%EB%8B%A8-CRUD-%EC%82%AC%EC%9A%A9"
---
**Spring Data JPA 라이브러리를 사용하여 데이터베이스에 연결해 보았다.**

#### **설정**

**1\. Gradle dependencies 설정**

```java
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	compileOnly 'org.projectlombok:lombok'
	developmentOnly 'org.springframework.boot:spring-boot-devtools'
	annotationProcessor 'org.projectlombok:lombok'
	providedRuntime 'org.springframework.boot:spring-boot-starter-tomcat'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
	// https://mvnrepository.com/artifact/org.mariadb.jdbc/mariadb-java-client
	implementation group: 'org.mariadb.jdbc', name: 'mariadb-java-client', version: '2.7.1'
}
```

**2\. properties 설정**

```java
spring.datasource.driver-class-name=org.mariadb.jdbc.Driver
spring.datasource.url=jdbc:mariadb://localhost:3306/bootex
spring.datasource.username=~
spring.datasource.password=~

spring.jpa.hibernate.ddl-auto=update
spring.jpa.properties.hibernate.format_sql=true
spring.jpa.show-sql=true
```

> **※ Spring Boot가 기본적으로 이용하는 커넥션 풀(Connection Pool)은 HikariCP 라이브러리를 사용**

* * *

#### **Entity Class (엔티티 클래스)와 JpaRepository**

**먼저 JPA를 통해서 관리하게 되는 객체(Entity(엔티티) 객체)를 위한 Entity Class를 만든다.**

**그리고 엔티티 객체들을 처리하는 기능을 가진 Repository를 만든다.**

#### **Entity Class (엔티티 클래스)**

**Entity Class는 DB의 테이블과 같은 구조로 볼 수 있다.**

```java
package org.zerock.ex2.entity;

import lombok.*;

import javax.persistence.*;

@Entity
@Table(name = "tbl_memo")
@ToString
@Getter
@Builder
@AllArgsConstructor
@NoArgsConstructor
public class Memo {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long mno;

    @Column(length = 200, nullable = false)
    private String memoText;
}
```

**아래 펼쳐보면, Hibernate를 통해서 테이블이 클래스의 내용대로 생성되는 것을 확인할 수 있다.**

더보기

```java
> Task :compileJava
> Task :processResources
> Task :classes

> Task :Ex2Application.main()
15:12:47.133 [Thread-0] DEBUG org.springframework.boot.devtools.restart.classloader.RestartClassLoader - Created RestartClassLoader org.springframework.boot.devtools.restart.classloader.RestartClassLoader@30057e31

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::      (v2.7.10-SNAPSHOT)

2023-03-07 15:12:47.442  INFO 2980 --- [  restartedMain] org.zerock.ex2.Ex2Application            : Starting Ex2Application using Java 11.0.17 on DESKTOP-D9M6GKR with PID 2980 (D:\workspace\springBootProject\ex2\ex2\build\classes\java\main started by ���غ� in D:\workspace\springBootProject\ex2)
2023-03-07 15:12:47.444  INFO 2980 --- [  restartedMain] org.zerock.ex2.Ex2Application            : No active profile set, falling back to 1 default profile: "default"
2023-03-07 15:12:47.490  INFO 2980 --- [  restartedMain] .e.DevToolsPropertyDefaultsPostProcessor : Devtools property defaults active! Set 'spring.devtools.add-properties' to 'false' to disable
2023-03-07 15:12:47.491  INFO 2980 --- [  restartedMain] .e.DevToolsPropertyDefaultsPostProcessor : For additional web related logging consider setting the 'logging.level.web' property to 'DEBUG'
2023-03-07 15:12:47.934  INFO 2980 --- [  restartedMain] .s.d.r.c.RepositoryConfigurationDelegate : Bootstrapping Spring Data JPA repositories in DEFAULT mode.
2023-03-07 15:12:47.946  INFO 2980 --- [  restartedMain] .s.d.r.c.RepositoryConfigurationDelegate : Finished Spring Data repository scanning in 5 ms. Found 0 JPA repository interfaces.
2023-03-07 15:12:48.720  INFO 2980 --- [  restartedMain] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2023-03-07 15:12:48.730  INFO 2980 --- [  restartedMain] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2023-03-07 15:12:48.730  INFO 2980 --- [  restartedMain] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.71]
2023-03-07 15:12:48.846  INFO 2980 --- [  restartedMain] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2023-03-07 15:12:48.846  INFO 2980 --- [  restartedMain] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 1354 ms
2023-03-07 15:12:49.021  INFO 2980 --- [  restartedMain] o.hibernate.jpa.internal.util.LogHelper  : HHH000204: Processing PersistenceUnitInfo [name: default]
2023-03-07 15:12:49.061  INFO 2980 --- [  restartedMain] org.hibernate.Version                    : HHH000412: Hibernate ORM core version 5.6.15.Final
2023-03-07 15:12:49.186  INFO 2980 --- [  restartedMain] o.hibernate.annotations.common.Version   : HCANN000001: Hibernate Commons Annotations {5.1.2.Final}
2023-03-07 15:12:49.255  INFO 2980 --- [  restartedMain] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Starting...
2023-03-07 15:12:49.308  INFO 2980 --- [  restartedMain] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Start completed.
2023-03-07 15:12:49.331  INFO 2980 --- [  restartedMain] org.hibernate.dialect.Dialect            : HHH000400: Using dialect: org.hibernate.dialect.MariaDB106Dialect
Hibernate: 
    
    create table tbl_memo (
       mno bigint not null auto_increment,
        memo_text varchar(200) not null,
        primary key (mno)
    ) engine=InnoDB
2023-03-07 15:12:49.807  INFO 2980 --- [  restartedMain] o.h.e.t.j.p.i.JtaPlatformInitiator       : HHH000490: Using JtaPlatform implementation: [org.hibernate.engine.transaction.jta.platform.internal.NoJtaPlatform]
2023-03-07 15:12:49.814  INFO 2980 --- [  restartedMain] j.LocalContainerEntityManagerFactoryBean : Initialized JPA EntityManagerFactory for persistence unit 'default'
2023-03-07 15:12:49.852  WARN 2980 --- [  restartedMain] JpaBaseConfiguration$JpaWebConfiguration : spring.jpa.open-in-view is enabled by default. Therefore, database queries may be performed during view rendering. Explicitly configure spring.jpa.open-in-view to disable this warning
2023-03-07 15:12:50.135  INFO 2980 --- [  restartedMain] o.s.b.d.a.OptionalLiveReloadServer       : LiveReload server is running on port 35729
2023-03-07 15:12:50.165  INFO 2980 --- [  restartedMain] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2023-03-07 15:12:50.175  INFO 2980 --- [  restartedMain] org.zerock.ex2.Ex2Application            : Started Ex2Application in 3.03 seconds (JVM running for 3.486)
```

```java
Hibernate: 
    insert 
    into
        tbl_memo
        (memo_text) 
    values
        (?)
Hibernate: 
    insert 
    into
        tbl_memo
        (memo_text) 
    values
        (?)
```

![](/assets/img/posts/189/1.png)

> **@Entity : 엔티티 클래스는 Spring Data JPA에서 반드시 @Entity라는 어노테이션을 추가해야 한다.**  
> **해당 클래스가 엔티티를 위한 클래스이며, 해당 클래스의 인스턴스들을 JPA로 관리되는 엔티티 객체라는 것을 의미  
>   
> @Table : @Entity 어노테이션과 같이 사용할 수 있는 어노테이션.  
> DB에서 엔티티 클래스를 어떠한 테이블로 생성할 것인지에 대한 정보를 담기 위한 어노테이션  
> (※ name 속성값이 없는 경우에는 클래스의 이름과 동일한 이름으로 테이블이 생성)  
>   
> @Id : @Entity가 붙은 클래스는 Primary Key에 해당하는 특정 필드를 @Id로 지정해야만 한다.  
> @GeneratedValue(strategy = GenerationType.IDENTITY) : PK를 자동으로 생성할 때 사용(키 생성 전략)  
>   
> ※ 키 생성 전략  
> AUTO(default) : JPA가 생성방식을 결정  
> IDENTITY : 사용하는 DB가 키 생성을 결정. (MySQL , MariaDB 인 경우에는 auto increment 방식)  
> SEQUENCE : DB의 sequence를 이용해서 키를 생성. @SequenceGenerator와 같이 사용  
> TABLE : 키 생성 전용 테이블을 생성해서 키 생성. @TableGenerator와 같이 사용  
>   
> @Column : 추가적인 필드(칼럼)가 필요한 경우에 사용하는 어노테이션.  
> (※ columnDefinition을 이용하면 기본값을 지정할 수 있다.)  
> @Transient : @Column과반대로 DB테이블에는 칼럼으로 생성되지 않는 필드의 경우에 사용하는 어노테이션.  
>   
> @Builder : 객체를 생성할 수 있게 처리해 주는 어노테이션.  
> (※ @AllArgsConstructor, @NoAllArgsConstructor를 항상 같이 처리해야 컴파일 에러가 발생하지 않음.)**

#### **JpaRepository**

**Spring Data JPA에는 여러 종류의 인터페이스 기능을 통해서 JPA 관련 작업을 별도의 코드 없이 처리할 수 있게 지원한다.**

**예를 들어, CRUD 작업, 페이징, 정렬 등의 처리도 인터페이스의 메서드를 호출하는 형태로 처리가 가능하다.**

```java
package org.zerock.ex2.repository;

import org.springframework.data.jpa.repository.JpaRepository;
import org.zerock.ex2.entity.Memo;

public interface MemoRepository extends JpaRepository<Memo, Long> {
}
```

**일반적으로 JpaRepository를 이용하는 것이 가장 무난한 선택이라고 한다.**

![](/assets/img/posts/189/2.png)

> **JpaRepository를 이용하여 CRUD 처리**  
>   
> **insert : save(entity 객체)**  
> **select : findById(entity 객체), getOne(entity 객체)**  
> **update : save(entity 객체)**  
> **delete : deleteById(entity 객체), delete(entity 객체)**  
>   
> **※ insert와 update 처리에 사용하는 메서드가 save()로 동일한 이유**  
> **\-> JPA의 구현체가 메모리상에서 객체를 비교를 먼저 하고 없다면 insert, 존재한다면 update를 동작시키는 방식**

* * *

#### **Test Code**

```java
package org.zerock.ex2.repository;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;
import org.zerock.ex2.entity.Memo;

import javax.transaction.Transactional;
import java.util.Optional;
import java.util.stream.IntStream;

@SpringBootTest
public class MemoRepositoryTests {

    @Autowired
    MemoRepository memoRepository;

    @Test
    public void testClass(){
        System.out.println(memoRepository.getClass().getName());
    }

    @Test
    public void testInsertDummies(){
        IntStream.rangeClosed(1,100).forEach(i -> {
            Memo memo = Memo.builder().memoText("Sample Text..." + i).build();
            memoRepository.save(memo);
        });
    }
}
```

* * *

#### **조회**

**findById() 사용**

```java
    @Test
    public void testSelect(){
        //데이터베이스에 존재하는 mno
        Long mno = 100L;
        Optional<Memo> result = memoRepository.findById(mno);

        System.out.println("==============================");

        if(result.isPresent()){
            Memo memo = result.get();
            System.out.println(memo);
        }
    }
```

```java
Hibernate: 
    select
        memo0_.mno as mno1_0_0_,
        memo0_.memo_text as memo_tex2_0_0_ 
    from
        tbl_memo memo0_ 
    where
        memo0_.mno=?
==============================
Memo(mno=100, memoText=Sample Text...100)
```

**getOne() 사용**

```java
    @Transactional
    @Test
    public void testSelect2(){
        //데이터베이스에 존재하는 mno
        Long mno = 100L;
        Memo memo = memoRepository.getOne(mno);
        System.out.println("==============================");

        System.out.println(memo);
    }
```

```java
==============================
Hibernate: 
    select
        memo0_.mno as mno1_0_0_,
        memo0_.memo_text as memo_tex2_0_0_ 
    from
        tbl_memo memo0_ 
    where
        memo0_.mno=?
Memo(mno=100, memoText=Sample Text...100)
```

> **fincById() vs getOne()**  
> **: 이 2가지는 select 하는 목적은 같지만, 동작하는 방식이 조금 다르다.**  
>   
> **fincById() - DB를 먼저 이용**  
> **getOne() - 실제 객체가 필요한 순간까지 SQL을 실행하지 않고, 필요할 때 이용**

* * *

#### **수정**

```java
    @Test
    public void testUpdate(){
        Memo memo = Memo.builder().mno(100L).memoText("Update Text").build();

        System.out.println(memoRepository.save(memo));
    }
```

```java
Hibernate: 
    select
        memo0_.mno as mno1_0_0_,
        memo0_.memo_text as memo_tex2_0_0_ 
    from
        tbl_memo memo0_ 
    where
        memo0_.mno=?
Hibernate: 
    update
        tbl_memo 
    set
        memo_text=? 
    where
        mno=?
Memo(mno=100, memoText=Update Text)
```

![](/assets/img/posts/189/3.png)

* * *

#### **삭제**

```java
    @Test
    public void testDelete(){
        Long mno = 100L;

        memoRepository.deleteById(mno);
    }
```

```java
Hibernate: 
    select
        memo0_.mno as mno1_0_0_,
        memo0_.memo_text as memo_tex2_0_0_ 
    from
        tbl_memo memo0_ 
    where
        memo0_.mno=?
Hibernate: 
    delete 
    from
        tbl_memo 
    where
        mno=?
```

![](/assets/img/posts/189/4.png)

* * *

#### **페이징**

**Spring Data JPA를 이용할 때 페이지 처리는 반드시 '0'부터 시작한다는 점을 기억해야 한다. ( 0 == 1페이지)**

**페이지 처리를 위한 가장 중요한 존재는 Pageable 인터페이스다.**

**페이지 처리에 필요한 정보를 전달하는 용도의 타입.** **구현체로는 PageRequest 클래스를 사용한다.**

**(org.springframework.data.domain.Pageable | org.springframework.data.domain.PageRequest)**

**PageRequest 클래스의 생성자는 protected로 선언되어 new를 이용할 수 없다. 객체를 생성하긴 위해서는 static 메서드인 of() 이용해서 처리해야 한다.**

```java
    @Test
    public void testPageDefault(){

        //1페이지, 10개
        Pageable pageable = PageRequest.of(0,10);

        Page<Memo> result = memoRepository.findAll(pageable);

        System.out.println(result);

        System.out.println("--------------------------------------");
        System.out.println("Total Pages : " + result.getTotalPages()); // 총 페이지
        System.out.println("Total Coubt : " + result.getTotalElements()); // 총 갯수
        System.out.println("Page Number : " + result.getNumber()); // 현재페이지
        System.out.println("Page Size : " + result.getSize()); // 페이지당 데이터 개수
        System.out.println("has next page? : " + result.hasNext()); // 다음페이지 존재여부
        System.out.println("first page? : " + result.isFirst()); // 시작페이지(0) 여부

        System.out.println("--------------------------------------");
        for(Memo memo : result.getContent()){
            System.out.println(memo);
        }
    }
```

```java
Hibernate: 
    select
        memo0_.mno as mno1_0_,
        memo0_.memo_text as memo_tex2_0_ 
    from
        tbl_memo memo0_ limit ?
Hibernate: 
    select
        count(memo0_.mno) as col_0_0_ 
    from
        tbl_memo memo0_
Page 1 of 10 containing org.zerock.ex2.entity.Memo instances
--------------------------------------
Total Pages : 10
Total Coubt : 99
Page Number : 0
Page Size : 10
has next page? : true
first page? : true
--------------------------------------
Memo(mno=1, memoText=Sample Text...1)
Memo(mno=2, memoText=Sample Text...2)
Memo(mno=3, memoText=Sample Text...3)
Memo(mno=4, memoText=Sample Text...4)
Memo(mno=5, memoText=Sample Text...5)
Memo(mno=6, memoText=Sample Text...6)
Memo(mno=7, memoText=Sample Text...7)
Memo(mno=8, memoText=Sample Text...8)
Memo(mno=9, memoText=Sample Text...9)
Memo(mno=10, memoText=Sample Text...10)
```

* * *

#### **정렬**

```java
    @Test
    public void testSort(){
        Sort sort1 = Sort.by("mno").descending();

        Pageable pageable = PageRequest.of(0,10, sort1);

        Page<Memo> result = memoRepository.findAll(pageable);

        result.get().forEach(memo -> {
            System.out.println(memo);
        });
    }
```

```java
Hibernate: 
    select
        memo0_.mno as mno1_0_,
        memo0_.memo_text as memo_tex2_0_ 
    from
        tbl_memo memo0_ 
    order by
        memo0_.mno desc limit ?
Hibernate: 
    select
        count(memo0_.mno) as col_0_0_ 
    from
        tbl_memo memo0_
Memo(mno=99, memoText=Sample Text...99)
Memo(mno=98, memoText=Sample Text...98)
Memo(mno=97, memoText=Sample Text...97)
Memo(mno=96, memoText=Sample Text...96)
Memo(mno=95, memoText=Sample Text...95)
Memo(mno=94, memoText=Sample Text...94)
Memo(mno=93, memoText=Sample Text...93)
Memo(mno=92, memoText=Sample Text...92)
Memo(mno=91, memoText=Sample Text...91)
Memo(mno=90, memoText=Sample Text...90)
```
