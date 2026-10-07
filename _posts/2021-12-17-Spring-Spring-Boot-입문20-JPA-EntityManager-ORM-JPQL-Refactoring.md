---
title: "[Spring] Spring Boot - 입문(20) - JPA + EntityManager + ORM + JPQL + Refactoring"
date: 2021-12-17 00:18:44 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["entitymanager", "jpa", "jpql", "persist", "refactoring", "스프링 jpa"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Boot-%EC%9E%85%EB%AC%B820-JPA-EntityManager-ORM-JPQL-Refactoring"
---
### **\[Spring\] Spring Boot - 입문(20) - JPA + EntityManager + ORM + JPQL + Refactoring** 

* * *

**JDBC -> JdbcTemplete로 넘어오면서 반복되는 코드들은 많이 줄어든 것을 저번 강의에서 배웠다.**

**하지만 아직도 문제?라고 보는건, 여전히 'SQL 쿼리'를 직접 짜야만 한다.**

**그래서 오늘은 이러한 '쿼리'를 자동으로 작성해주는 'JPA'에 대해서 알아보는 내용이다.**

### **JPA**

-   **기존의 반복 코드는 물론이고, 기본적인 SQL도 JPA가 직접 만들어서 실행해준다.**
-   **JPA를 사용하면, SQL과 데이터 중심의 설계에서 객체 중심의 설계로 전환할 수 있다.**
-   **JPA를 사용하면 개발 생산성을 크게 높일 수 있다.**

**JPA는 '인터페이스'이다. 그 밑에 구현체들 중에서 'hibernate' 등 여러개의 기술들이 있다.**

**JPA는 객체랑 ORM (Object Relational Mapping)-객체 관계 매핑이라는 기술이다.**

**즉, '객체'와 '관계형 데이터베이스'를 매핑한다는 뜻이다.**

**매핑은 '어노테이션'으로 진행한다.**

#### **1\. JPA사용을 위한 build.gralde 설정 추가**

**jdbc는 사용하지 않을 거라서, 삭제하거나 주석처리를 해주고 jpa관련 dependency를 삽입해주자.**

```java
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	//implementation 'org.springframework.boot:spring-boot-starter-jdbc' 
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	//12-16일 추가 - JPA 사용
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```

#### **2\. application,properties 설정 추가**

```java
spring.jpa.show-sql=true

spring.jpa.hibernate.ddl-auto=none
```

**spring.jpa.show-sql=true : JPA가 날리는 SQL 쿼리를 볼 수 있다.**

**spring.jpa.hibernate.ddl-auto=none : 객체를 보고 자동으로 테이블을 생성해주는 기능 끄기 (이미 있기 때문에)**

#### **3\. 매핑을 위한 Member 수정**

**JPA를 사용하려면 엔티티라는 것을 매핑해야한다.**

```java
@Entity // 이 어노테이션이 있으면 JPA가 관리하는 '엔티티'로 인식
public class Member {

	@Id @GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;
	@Column(name="name")
	private String name;
	
	public Long getId() {
		return id;
	}
	public void setId(Long id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
}
```

-   **@Id : PK 매핑**
-   **@GeneratedValue(strategy = GenerationType.IDENTITY) :**  
    **PK값을 DB가 자동으로 생성해주는 방법을 'IDENTITY' 전략이라고 한다.**
-   **@Column(name="DB 칼럼명") : DB에 지정된 칼럼명으로 맞춰서 작성한다.  
    \=> @Column(name="name")  
    **

**이 어노테이션을 통해서 JPA가 쿼리를 알아서 자동으로 작성해준다!!!**

#### **4\. Repository 생성**

```java
public class JpaMemberRepository implements MemberRepository{

	private final EntityManager em; 
	// JPA는 EntityManager라는 걸로 모든게 동작한다.
	// 스프링 부트가 자동으로 DB내용과 연관해서 'EntityManager'를 생성해준다.
	// 'EntityManager'는 내부적으로 DataSource를 들고있어서 DB와 통신하는걸 내부적으로 처리해준다.
	
	public JpaMemberRepository(EntityManager em) {
		this.em = em;
	}
	@Override
	public Member save(Member member) {
		em.persist(member); //이렇게만 작성하면 member값을 넣어주고 id값도 자동으로 넣어서 Save해준다.
		return member;
	}
	@Override
	public Optional<Member> findById(Long id) {
		Member member = em.find(Member.class, id);
		return Optional.ofNullable(member);
	}
	@Override
	public Optional<Member> findByName(String name) {
		List<Member> result = em.createQuery("SELECT m from Member m where m.name = :name", Member.class)
		.setParameter("name",name)
		.getResultList();
		return result.stream().findAny();
	}
	@Override
	public List<Member> findAll() {
		return em.createQuery("SELECT m from Member m", Member.class).getResultList();
		// JPQL을 사용하여 객체를 대상으로 쿼리를 날린다.
	}
}
```

**단순 저장은 'persist'만 사용해서 데이터를 저장할 수 있다.**

**PK값을 조회하는 것은 'find'를 사용해서 조회할 수 있지만,** 

**PK값이 아닌 다른 '칼럼'들을 조회할 때는, 'JPQL'을 사용해야 한다.**

**거의 SQL가 비슷하지만, 차이점이라면 SQL은 테이블 대상으로 쿼리를 날리는 것이라면,**

**JPQL은 객체(엔티티)를 대상으로 쿼리를 날린다.**

#### **5\. Sevice에 어노테이션 추가**

**※ JPA를 사용하려면 서비스 계층에 '@Transactional' 어노테이션을 사용해야 한다.** 

![](/assets/img/posts/39/1.png)

#### **6\. SpringConfig 설정 변경**

```java
	@Configuration 
	public class SpringConfig {
    
	private EntityManager em; 
    
	@Autowired
	public SpringConfig(EntityManager em) {
		this.em = em;
	}
    
    
   	 @Bean
	public MemberRepository memberRepository() {
	return new JpaMemberRepository(em);
	}
```

**EntityManager내부에 DataSource가 있기 때문에 EntityManager만 주입받으면 된다.**

#### **7\. 테스트**

**결과**

![](/assets/img/posts/39/2.png)

*좋다...!*

* * *

**Tip** 

**Refactoring 기술**

**※ 'result'를 Shift + Alt + I를 누르면 하나로 합쳐준다. - Inline variable**

```java
	// 변환 전
	@Override
	public List<Member> findAll() {
		List<Member> result = em.createQuery("SELECT m from Member m", Member.class).getResultList();
		return result;
	}
    
  	// 변환 후
        @Override
	public List<Member> findAll() {
		return em.createQuery("SELECT m from Member m", Member.class).getResultList();
	}
```
