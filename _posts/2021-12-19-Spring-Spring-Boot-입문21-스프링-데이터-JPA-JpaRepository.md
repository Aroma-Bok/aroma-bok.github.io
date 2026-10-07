---
title: "[Spring] Spring Boot - 입문(21) - 스프링 데이터 JPA + JpaRepository"
date: 2021-12-19 15:51:18 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["jparespository", "스프링 api", "스프링 jpa", "스프링 데이터 jpa"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Boot-%EC%9E%85%EB%AC%B821-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%8D%B0%EC%9D%B4%ED%84%B0-JPA-JpaRepository"
---
### **스프링 데이터 JPA + JpaRepository**

* * *

**스프링 부트와 JPA만 사용해도 개발 생산성이 늘어나지만, 스프링 JPA까지 사용한다면 한계를 넘어설 수 있도록 도와준다고 한다. 스프링 부트와 JPA라는 기반 위에 스프링 데이터 JPA 프레임워크까지 사용한다면 개발이 너무나도 즐거워진다고 한다. 그리고 반복 개발하던 기본 CRUD 기능도 스프링 데이터 JPA가 모두 제공한다고 한다.**

**이제는 실무에서 관계형 데이터베이스를 사용한다면, 스프링 데이터 JPA는 이제 선택이 아니라 '필수'라고 한다.**

**※주의 : 스프링 데이터 JPA는 JPA를 편리하게 사용하도록 도와주는 기술이다. 따라서 JPA를 먼저 학습한 후에 스프링 데이터 JPA를 학습하는 것을 권장한다고 한다.**

### **1\. 스프링 데이터 JPA 리포지터리 생성**

```html
package hello.hellospring.repository;
import java.util.Optional;
import org.springframework.data.jpa.repository.JpaRepository;
import hello.hellospring.domain.Member;

public interface SpringDataJpaMemberRepository extends JpaRepository<Member, Long>, MemberRepository{
	@Override Optional<Member> findByName(String name);
}
```

**놀랍지만, 지금까지 만들었던 어떤 Repository보다 쉽고 간단하다. 그냥 '인터페이스'만으로 기능 구현이 끝이 났다.**

**기존에 작성했던, 'save', 'findById', 'findAll' 기능들은 만들 필요가 없다!!** 

**'JpaRepository'가 이미 기능을 포함하고 있기 때문에 우리 입맛에 필요한 기능만 넣어주면 된다!**

#### **원리 :** 

**1\. 'JpaRepository'를 상속받고 있으면, 구현체를 자동으로 만들어준다.**

**2\. 그리고 '스프링 데이터 JPA'가 'SpringDataJpaMemberRepository'를 보고 자동으로 Bean에 등록해준다.**

### **2\. SpringConfig 설정 변경**

```html
package hello.hellospring;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import hello.hellospring.repository.MemberRepository;
import hello.hellospring.service.MemberService;

@Configuration
public class SpringConfig {

	private final MemberRepository memberRepository;

	@Autowired
	public SpringConfig(MemberRepository memberRepository) {
		this.memberRepository = memberRepository;
	}
	@Bean
	public MemberService memberService() {
		return new MemberService(memberRepository);

	}
}
```

#### **원리**

**1\. 'MemberRepository'를 DI 해준다.**

**2\. 주입받은 'MemberRepository'를 'MemberService'에 넣어준다.**

#### **3\. 테스트**

**결과는 초록불이고 잘 돌아간다.**

![](/assets/img/posts/40/1.png)

*통합결과 확인*

**콘솔을 확인해보면, 'Hibernate'가 뜨는 걸 볼 수 있는데, 당연하다. '스프링 데이터 JPA'가 끌어다 쓰기 때문에 같은 라이브러리를 쓰고 있는 것이다.**

![](/assets/img/posts/40/2.png)

*Hibernate*

* * *

**Q. Save, findByID, findAll은 'SpringDataJpaMemberRepository'에 정의하지 않았는데 어떻게 쓸 수 있는 걸까?**

**바로 'JpaRepository'를 상속받기 때문이다. 아래를 보면 'JpaRepository'가 기본적으로 필요한 기능들을 포함하고 있는 것을 알 수 있다. 그렇기 때문에 반복적으로 사용하는 'CRUD'기능은 따로 안 만들어도 된다.**

**대신에 'findByName'처럼 공통기능이 아닌 기능은 만들어서 사용해야 한다.**

![](/assets/img/posts/40/3.png)

*공통으로 사용되는 기능들은 이미 포함하고 있다.*

![](/assets/img/posts/40/4.png)

*JpaRepository 소스코드를 보면 이미 만들어진 기능들을 확인할 수 있다.*

**※ 참고 : 실무에서는 JPA와 스프링 데이터 JPA를 기본으로 사용하고,** **복잡한 동적 쿼리는 Querydsl이라는 라이브러리를 사용하면 된다.**

**Querydsl을 사용하면 쿼리도 자바 코드로 안전하게 작성할 수 있고, 동적 쿼리도 편리하게 작성할 수 있다.**

**이 조합으로 해결하기 어려운 쿼리는 JPA가 제공하는 네이티브 쿼리를 사용하거나, 앞서 학습한 JdbcTemplate를 사용하면 된다.**
