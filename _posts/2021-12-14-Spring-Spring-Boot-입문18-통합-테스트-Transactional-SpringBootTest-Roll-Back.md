---
title: "[Spring] Spring Boot - 입문(18) - 통합 테스트 + @Transactional + @SpringBootTest + Roll Back + 단위 테스트 + 통합 테스트"
date: 2021-12-14 23:43:07 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["@sprngboottest", "@transactional", "springboot", "단위테스트", "스프링", "스프링 부트", "통합테스트"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Boot-%EC%9E%85%EB%AC%B818-%ED%86%B5%ED%95%A9-%ED%85%8C%EC%8A%A4%ED%8A%B8-Transactional-SpringBootTest-Roll-Back-%EB%8B%A8%EC%9C%84-%ED%85%8C%EC%8A%A4%ED%8A%B8-%ED%86%B5%ED%95%A9-%ED%85%8C%EC%8A%A4%ED%8A%B8"
---
### **통합 테스트**

**기존에 배웠던 TEST 방식은 '순수 Java 코드'로 실행시켰다.**

**하지만 DB에 연결하면서 '순수 Java 코드'만으로는 TEST를 할 수가 없다.**

**그래서 이제는 Spring과 엮어서 TEST를 하는 방법을 배워보자.**

* * *

#### **1\. TEST 코드 작성** 

```java
package hello.hellospring.service;
import static org.junit.jupiter.api.Assertions.*;

@SpringBootTest // 스프링이 테스트를 할 때 사용하는 것
@Transactional  // 메모리를 리셋해주는 역할을 대신해주는 어노테이션
class MemberServiceIntergrationTest {
	
	@Autowired MemberService memberService;
	@Autowired MemberRepository memberRepository; // 구현체를 끌어올 수 있다.

	@Test
	void 회원가입() {
		
		//given 
		Member member = new Member();
		member.setName("Hi2");
		
		//when
		Long saveId = memberService.join(member);
		
		//then
		Member findMember = memberService.findOne(saveId).get(); 
		
		Assertions.assertThat(member.getName()).isEqualTo(findMember.getName());
	}
	
	@Test
	public void 중복() {
		//given
		Member member1 = new Member();
		member1.setName("spring141");
		
		Member member2 = new Member();
		member2.setName("spring145");
		
		//when
		memberService.join(member1);
		
		IllegalStateException e = assertThrows(IllegalStateException.class, () -> memberService.join(member2));
		
		Assertions.assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원");

		//then
	}
}
```

#### **어노테이션 설명**

**@SpringBootTest : 스프링 컨테이너와 테스트를 함께 실행한다.**

**@Transactional : 테스트 케이스에 이 어노테이션이 있으면, 테스트 시작 전에 트랜잭션을 시작하고,**

**테스트 완료 후에 항상 RollBack한다. 이렇게 하면 DB에 데이터가 남지 않으므로 테스트에 영향을 주지 않는다.**

**(Service에 들어가 있으면 RollBack 하지 않고 정상적으로 돌아간다.)**

* * *

**배웠던 내용을 바탕으로 아래처럼 Spring이 실행되면 바로 받아올 수 있게끔 코드를 작성해주자.**

**@Autowired MemberService memberService;**

**@Autowired MemberRepository memberRepository;**

**@BeforeEach, @AfterEach는 이제 불필요하기 때문에 지우고 다른 방법으로 테스트를 진행할 것이다.**

**실행하면 기존의 테스트와는 달리, Spring이 실행되면서 테스트가 진행되는 것을 볼 수 있다.**

![](/assets/img/posts/37/1.png)

*Spring이 실행되면서 Junit 실행*

![](/assets/img/posts/37/2.png)

*회원가입 성공!!!*

**강사님이 전에도 말씀하셨다. "테스트는 반복을 할 수 있어야 한다." 똑같은 내용으로 Insert를 하더라도 계속 반복이 돼야 한다는 말이다. 그래서 기존 테스트 코드를 작성할때는 @BeforeEach, @AfterEach를 사용하면서 메모리에 올라간 데이터를 리셋해주는 방식을 사용했었다.**

**회원가입을 처음 실행하면 성공하지만, 2번째 돌렸을 때는 에러가 발생한다.**

![](/assets/img/posts/37/3.png)

**DB는 트랜잭션의 개념이 있기 때문에, Commit을 해야 DB에 반영이 된다고 한다.** 

**그래서 자동으로 TEST하고나서 RollBack을 하면 DB에 반영이 안 되고 처음 상태로 되돌아오기 때문에 반복이 가능하다!**

**그 기능을 해주는 것이 '@Transactional' 어노테이션이다.**

![](/assets/img/posts/37/4.png)

*테스트에 정의한 어노테이션*

![](/assets/img/posts/37/5.png)

![](/assets/img/posts/37/6.png)

**'@Transactional' 어노테이션을 사용하면 반복적으로 테스트가 가능한 것을 볼 수 있다.**

**그러면, '순수 Java코드'를 이용해서 하는 테스트는 필요 없는건가? 라는 생각이 들 수 있다.**

**단위 테스트 : '순수 Java코드'만으로 이루어져서 최소한의 단위로 나눈 테스트**

**통합 테스트 : '컨테이너' & 'DB'를 연동하면서 같이 테스트하는 방식**

**가급적이면 '순수 Java코드'로 이루어진 '단위 테스트'가 훨씬 좋은 테스트일 확률이 높다.**

**단위 단위 쪼개고, 스프링 컨테이너 없이 테스트를 돌릴 수 있도록 훈련을 해야 한다.**

**"꼭 좋은 테스트라곤 할 순 없지만, 좋은 테스트일 확률이 높다." 라고 말씀하셨다.**
