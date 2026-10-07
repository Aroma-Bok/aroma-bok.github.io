---
title: "[Spring] Spring Framework - 핵심 원리 (6)- 도메인 실행과 테스트 +"
date: 2022-01-11 22:12:54 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["스프링 dip", "스프링 의존관계"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-6-%EB%8F%84%EB%A9%94%EC%9D%B8-%EC%8B%A4%ED%96%89%EA%B3%BC-%ED%85%8C%EC%8A%A4%ED%8A%B8"
---
### **도메인 실행과 테스트 + DIP + Junit 테스트**

**런타임에 동작을 하면, 클라이언트는 회원서비스인 'MemberServiceImpl'을 사용하게 된다.**

**그다음에 실제로 'MemberServiceImpl'에서 'new MemoryMemberRepository();'를 통해서 넣어서 참조하고 있다.**

![](/assets/img/posts/64/1.png)

*동적인 객체 다이어그램*

* * *

### **실행과 테스트 - 순수 Java**

#### **1\. 회원 도메인 - 회원 가입 main**

```javascript
public class MemberApp {

	public static void main(String[] args) {
		MemberService memberService = new MemberServiceImpl();
		Member member = new Member(1L, "memberA", Grade.VIP);
		memberService.join(member);
		
		Member findMember = memberService.findMember(1L);
		System.out.println("new member = " + member.getName());
		System.out.println("find Member = " + findMember.getName());
	}
}
```

![](/assets/img/posts/64/2.png)

**위 코드는 '순수한 Java'이다. '스프링' 관련된 건 1도 없다.**

**하지만, 이 방법은 좋은 방법이 아니다. 이번엔 'JUnit'을 사용해서 테스트를 해보려고 한다.**

* * *

### **실행과 테스트 - JUnit**

#### **1\. 회원 도메인 테스트**

**테스트를 진행할 때, 유용한 'given / when / then' 방법을 사용해보자.** 

**given : 무언가 주어졌을 때,** 

**when : ~할 때**

**then : 결과가 ~다**

**그리고 '검증'을 위해서 'Assertions - org.assertj.core.api.Assertions' 기능을 사용해서 검증하자.**

**실제 변수와 검증하는 변수가 같은지 다른지를 체크해준다.**

더보기

**Assertions - 프로그램의 특정 지점에 위치한 Assertions은 해당 지점에서 개발자가 반드시 참(true)이어야 한다고 생각하는 사항을 표현한 논리식  
**

```javascript
public class MemberServiceTest {
	MemberService memberService = new MemberServiceImpl();
	@Test
	void join() {
		//given : 무언가 주어졌을 때
		Member member = new Member(1L, "memberA", Grade.VIP);
		
		//when : ~할 때
		memberService.join(member);
		Member findMember = memberService.findMember(1L); // 내가 찾은 것
		
		//then : 결과가 ~다
		Assertions.assertThat(member).isEqualTo(findMember); // 'Assertions'통해서 찾은 것 
	}
}
```

![](/assets/img/posts/64/3.png)

*녹색불!!!!!*

**여기서 중요한 것은, '순수 Java'와 'JUnit'의 차이점을 아는 것이다.**

**'순수 Java'는 우리가 눈으로 직접 보면서 검증을 해야 한다.** 

**'JUnit'은 성공과 실패의 결과를 한눈에 알려주기 때문에 훨씬 수월하다.**

**요즘 시대에 '테스트 코드'는 필수다!!**

* * *

**하지만, 여기서 위와 같은 설계의 문제점은 무엇일까? 생각해보자.**

-   **이 코드의 설계상 문제점은 무엇일까?**
-   **다른 저장소로 변경할 때 'OCP 원칙'을 잘 준수할까?**
-   **DIP를 잘 지키고 있을까?**

**즉,** **의존관계가 인터페이스뿐만 아니라 구현까지 모두 의존하는 문제가 있다**

**코드로 돌아가 보자. 'MemberServiceImpl'에서 'MemberRepository'라는 '인터페이스'에 의존하고 있다.**

**하지만, 할당하는 부분에서 구현체인 'MemoryMemberRepository'를 의존하고 있는 게 문제이다.**

**즉, 추상화에도 의존하고, 구체화에도 의존한다. = DIP를 위반하고 있다.**

```javascript
public class MemberServiceImpl implements MemberService{

	private final MemberRepository memberRepository = new MemoryMemberRepository();
```

**좀 더 구체적인 내용은 다음 정리 때 알아보자!**
