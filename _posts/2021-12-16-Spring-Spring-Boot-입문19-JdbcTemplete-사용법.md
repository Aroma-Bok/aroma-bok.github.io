---
title: "[Spring] Spring Boot - 입문(19) - JdbcTemplete 사용법 + 디자인패턴 + RowMapper"
date: 2021-12-16 22:27:24 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["jdbctemplate", "디자인패턴", "스프링", "스프링 부트", "스프링 입문 및 초보", "테스트 코드의 중요성"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Boot-%EC%9E%85%EB%AC%B819-JdbcTemplete-%EC%82%AC%EC%9A%A9%EB%B2%95"
---
### **\[Spring\] Spring Boot - 입문(19) - JdbcTemplete 사용법**

* * *

**JdbcTemplete :  MyBatis랑 비슷한 라이브러리**

**Jdbc APIT에서 반복적인 코드를 제거해준다. (중복을 제거)**

**하지만 SQL문은 직접 작성해야 한다. JdbcTemplete은 실무에서도 많이 쓰인다고 한다.**

**※ 여기서 'Templete'은 '디자인패턴' 중에서 'TempleteMethod'가 있는데 그 부분이 많이 들어가 있어서라고 한다.**

#### **1\. build.gradle에 dependency 설정 필요**

```java
implementation 'org.springframework.boot:spring-boot-starter-jdbc'
```

#### **2\. JdbcTempleteMemberRepository 생성 +  findById + RowMapper 생성**

**저번에 작성했던 JdbcMemberRepository와 비교해 봤을 때, 확연하게 코드가 줄어든 것을 볼 수 있다.**

**jdbcTemplate.query에서 사용할 RowMapper만 만들어주면 길었던 코드는 단 2줄로 끝난다....!!**

```java
	@Override
	public Optional<Member> findById(Long id) {
		List<Member> result = jdbcTemplate.query("SELECT * from member where id =?",memberRowMapper());
		return result.stream().findAny();
	}
```

```java
	private RowMapper<Member> memberRowMapper(){
		return new RowMapper<Member>() {
			@Override
			public Member mapRow(ResultSet rs, int rowNum) throws SQLException {
				
				Member member = new Member();
				member.setId(rs.getLong("id"));
				member.setName(rs.getString("name"));
				
				return member;
			}
		};
	}
```

**기존에 작성했던 JdbcMemberRepository에 'findById'와 비교해 보았다.**

![](/assets/img/posts/38/1.png)

*JdbcTempleteMemberRepository*

![](/assets/img/posts/38/2.png)

*JdbcMemberRepository*

#### **3\. save** 

**Insert부분에서는 신기한 기능이 있었다.** 

**'SimpleJdbcInsert'기능을 빌려서, '테이블명'과 'PK'만 알고 있다면 쿼리를 짜지 않아도 자동으로 Insert할 수 있다.**

```java
	@Override
	public Member save(Member member) {
		SimpleJdbcInsert jdbcInsert = new SimpleJdbcInsert(jdbcTemplate);
		jdbcInsert.withTableName("member").usingGeneratedKeyColumns("id");
		//SimpleJdbcInsert 기능을 빌려서, '테이블명'과 'PK'만 알고있다면 쿼리를 짜지 않아서 Insert할 수 있다.
		
        Map<String, Object> parameters = new HashMap<>();
        parameters.put("name", member.getName());
        
        Number key = jdbcInsert.executeAndReturnKey(new MapSqlParameterSource(parameters));
        member.setId(key.longValue());
        return member;
	}
```

#### **4\. findByName**

**findById와 다른 게 없다. 2줄이면 코드가 끝난다.**

```java
	@Override
	public Optional<Member> findByName(String name) {
		List<Member> result = jdbcTemplate.query("SELECT * from member where name =?",memberRowMapper());
		return result.stream().findAny();
	}
```

#### **5\. findAll**

```java
	@Override
	public List<Member> findAll() {
		return jdbcTemplate.query("SELECT * from member ",memberRowMapper());
	}
```

#### **6\. 조립 -> SpringConfig에서 연결되는 Repository 변경**

```java
	@Bean
	public MemberRepository memberRepository() {
		return new JdbcTempleteMemberRepository(dataSource);
	}
```

#### **7\. 테스트**

**테스트를 만들필요가없다. 이미 '통합 테스트'를 만들어 놓았기 때문에 그걸 이용하면 된다.**

**1차 시도에 바로 에러 발생!!** 

![](/assets/img/posts/38/3.png)

**Repository에서 findById, findByName에 각각 파라미터를 넣어주는 것을 빼먹었기 때문에 에러가 발생했었다.**

**테스트를 안돌리고 했었으면, 웹에서 확인하고 에러 찾고 이런 시간이 더 오래 걸렸을 것이다.** 

**확실히 테스트를 돌려봐야 하는 이유가 있다!!**

![](/assets/img/posts/38/4.png)

**다시 테스트를 실행해보면, 2개다 초록불이 들어온다!!!**

![](/assets/img/posts/38/5.png)

**DB랑 연동해서 확인이 다 된것이다. 이렇게 초록불이 들어온다면 기능으로 바로 구현해도 된다!**

**JdbcTemplate에 대해서는 더 디테일한 사용법은 직접 찾아보면 많이 알 수 있을 것이다!!**

**나중에 더 깊게 공부해보고 싶은 라이브러리다...!!**
