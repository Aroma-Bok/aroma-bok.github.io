---
title: "[Spring] Spring Boot - 입문(17) -Jdbc Repository 구현 + 객체지향적 설계 + OCP"
date: 2021-12-13 23:41:38 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["dip", "isp", "lsp", "ocp", "solid", "srp", "객체지향 5대 원리", "스프링", "스프링부트", "스프링의 매력"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Boot-%EC%9E%85%EB%AC%B817-Jdbc-Repository-%EA%B5%AC%ED%98%84-%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5%EC%A0%81-%EC%84%A4%EA%B3%84-OCP"
---
#### **Jdbc 리포지토리 구현**

DB에 INSERT, SELECT 쿼리를 날리는 방법을 배워보자.

대신에 오늘은 순수 JDBC 방법이라 머리가 많이 아프고 스트레스가 원초적인 방법이라고 한다.

편하게 다가가자!! 요즘엔 많이 발전했다!!

주의! 

이렇게 JDBC API로 직접 코딩하는 것은 20년 전 이야기이다. 

따라서 고대 개발자들이 이렇게 고생하고 살았구나 생각하고, 정신건강을 위해 참고만 하고 넘어가자.

* * *

#### **1\. 환경설정 - build.gradle파일에 jdbc, h2 데이터베이스 관련 라이브러리 추가**

![](/assets/img/posts/36/1.png)

*gradle 파일*

**implementation 'org.springframework.boot:spring-boot-starter-jdbc'**

**기본적으로 JAVA가 DB와 연동되려면 JDBC 드라이버가 꼭 있어야 한다.**

**runtimeOnly 'com.h2 database:h2'**

**DB랑 연동될 때, DB가 제공하는 클라이언트가 필요한데 그 역할을 해준다.**

**2\. DB 접속 정보 설정**

**옛날엔 DB 접속 정보를 직접 다 기입해야 했지만, 요즘엔 '경로'만 넣으면 스프링 Boot가 다 알아서 처리해준다고 한다.**

application.poperties 에 내용 삽입.

```java
spring.datasource.url=jdbc:h2:tcp://localhost/~/test
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa\u3141
```

원래 여기에 'ID' & 'PW' 도 입력을 해야 하지만, H2는 필요 없기 때문에 생략.

\* jdbc:h2:tcp://localhost/~/test 은 H2 콘솔을 실행하면 보이는 'JDBC URL'이다.

![](/assets/img/posts/36/2.png)

여기까지만 해도 스프링이 알아서 다 연결해준다.

이제 JDBC API 가지고 개발을 해보자.

현재 Interface는 MemberRepository인데,

**1) 이걸 메모리에 저장하는 구현체를 만들 것인가?**

**2) DB에 연결하는 구현체를 만들 것인가?** 

이 차이로 보면 된다.

#### **1\. JDBC를 이용하여 Repository 구성하기 - DB로 연결**

```java
package hello.hellospring.repository;

import org.springframework.jdbc.datasource.DataSourceUtils;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.List;
import java.util.Optional;
import javax.sql.DataSource;

import hello.hellospring.domain.Member;

public class JdbcMemberRepository implements MemberRepository {

	private final DataSource dataSource; // DB에 붙으려면 DataSource라는게 필요하다.

	public JdbcMemberRepository(DataSource dataSource) { // 스프링으보부터 DataSource를 주입 받아야한다.
		this.dataSource = dataSource;
		
		/*dataSource.getConnection();*/ 
		// dataSource.getConnection() : 새로운 커넥션이 주어지게 된다.
		// 스프링 프레임웍을 사용한다면, 이 방법이 아닌, DataSourceUtils을 통해서 커넥션을 연결하는게 좋다.
		// 그래야 똑같은 트랜잭션을 유지해준다고 한다.(???)
	}

@Override
	public Member save(Member member) {
		String sql = "insert into member(name) values(?)";
		
		Connection conn = null;
		PreparedStatement pstmt = null;
		ResultSet rs = null; // ResultSet : 결과는 받는 것

		try {
			conn = getConnection();
			pstmt = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS);
			// RETURN_GENERATED_KEYS : Insert해서 얻은 ID값 받아오기
			
			pstmt.setString(1, member.getName()); // '?'에 member.getName()을 통해서 값 넣기
			
			pstmt.executeUpdate(); //DB에 실제 쿼리 날림
			
			rs = pstmt.getGeneratedKeys(); //생성된 키 받아옴
			
			if (rs.next()) { // next() : 값이 있는지 파악
				member.setId(rs.getLong(1));
			} else {
				throw new SQLException("id 조회 실패");
			}
			return member;
		} catch (Exception e) {
			throw new IllegalStateException(e);
		} finally {
			close(conn, pstmt, rs); 
			// 사용하고나면 DB와 연결된 리소스 끊기 (안 끊으면 큰일... DB 커넥션이 계속 쌓인다고함. 에러 발생 주범)
		}

	}
```

**\* 소스코드가 길어서 중복되는 내용은 삭제하고, 필요한 부분만 정리**

#### **코드 분석**

1\. **private final DataSource dataSource : DB에 붙으려면 DataSoruce라는 게 필요하다. (일단은 이렇게만 알아두자)**

**2\. public JdbcMemberRepository(DataSource dataSource) {this.dataSource = dataSource; }  
:  **생성자가 뜨면서 **DataSource**  필요하다. 나중에 스프링으로부터 DataSource를 주입받아야 한다.****

****3\.**** **String sql = "insert into member(name) values(?)" : SQL문 작성, 파라미터 바인딩을 위해서 '?'로 취급**

**4\. conn = getConnection() : DB커넥션을 갖고 온다.**

**5\. pstmt = conn.prepareStatement(sql, Statement.RETURN\_GENERATED\_KEYS) :   
(SQL문, AUTO\_INCREMENT 키) RETURN\_GENERATED\_KEYS : Insert 해서 얻은 ID값 받아오기  
**

**6\.** **pstmt.setString(1, member.getName()) : (파라미터 인덱스 , 파라미터) / '?'에 member.getName()을 통해서 값 넣기  
7\. pstmt.executeUpdate() : sql 날리기  | if Insert 경우, executeQuery() 사용  
  
**

**이제 여기까지 했으면, config를 설정해야 한다.**

**기존에 Bean으로 저장했던, MemoryMemberRepository를 빼고, 새로운 JdbcMemberRepository를 넣어준다.**

```java
	@Bean
	public MemberRepository memberRepository() {
		   return new JdbcMemberRepository(dataSource);
	   //JdbcMemberRepository로 변경
	   //필요한 DataSource는 스프링이 제공해준다.(위에 Autowired로 얻어옴)   
	}
```

**필요한 dataSource는 Spring을 통해서 제공받아보자.** **(방법은 2가지)**

```java
	//방법1 : @Autowired DataSource dataSource;
	
	//방법2 : 
	private DataSource dataSource;
	
	@Autowired
	public SpringConfig(DataSource dataSource) {
		super();
		this.dataSource = dataSource;
	}
```

**여기서 중요한 건, 어떤 코드도 변경하지 않았고, JdbcMemberRepository를 만들어서 구현체를 확장했고,**

**Configuration부분만 설정해주었을 뿐이다.**

#### **결과**

![](/assets/img/posts/36/3.png)![](/assets/img/posts/36/4.png)

*DB에 저장되는 회원목록!!!*

![](/assets/img/posts/36/5.png)

*H2 DB에도 늘어났다*

* * *

### **이번 내용에서 중요한 점 :**

흔히 "스프링'을 왜 쓰냐?" 하면은 '**객체지향적인 설계**'가 좋다, 좋다 하는데, 오늘 배운 부분이 그 중요한 내용이다.

즉, "**다형성을 활용한다**"이다. '인터페이스'를 두고, 구현체를 바꿔치기를 할 수 있다.

스프링 컨테이너가 이걸 굉장히 쉽게 되도록 지원해주고 있다고 한다.

더 세밀하게 말하면, **DI(Dependency Injection)을 지원**하기 때문에 편리하게 한다고 한다!

\*과거에는 의존되는 코드들을 전부 변경해야 하는 일이 많았다고 한다..

\*오늘 공부한 코드는 거의 네버 쓸 일이 없기 때문에 몰라도 걱정 없다고 한다. 필자는 궁금해서 꼼꼼하게 적어본 것이다.

**설명**

![](/assets/img/posts/36/6.png)

MemberService는 MemberRepository를 의존하고 있다. 그리고 2개의 Repository가 존재한다.

![](/assets/img/posts/36/7.png)

기존에 사용하던 memberRepository를 빼고, 새롭게 만든 Jdbc를 끼워 넣었다.

아래 객체지향 개발 5대 원리 중에서 'O'에 해당하는 OCP에 대한 내용을 오늘 중점으로 배운 것 같다.

**객체지향 개발 5대 원리 : SOLID**

1\. SRP-Single Responsibility Principle (단일 책임의 원칙)

**2\. OCP- Open-Closed Principle (개방-폐쇄 원칙) : 확장에는 열려있고, 수정, 변경에는 닫혀있다.**

**풀어서 설명하면, 스프링의 DI (Dependencies Injection)을 사용하면 기존 코드를 전혀 손대지 않고,**

**설정만으로 구현 클래스를 변경할 수 있다.**

3\. LSP - The Liskov Substitution Principle (리스코프 치환의 원칙)

4\. ISP - Interface Segregation Principle (인터페이스 분리의 원칙)

5\. DIP - Dependency Inversion Principle (의존성 역전의 원칙)

**스프링의 매력 : 인터페이스의 구현체를 바꿔도 기존의 코드를 바꾸지 않고, 변경할 수 있는 것!**
