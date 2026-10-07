---
title: "[Spring] Spring Boot - 입문(23) - AOP적용 + Proxy(프록시)"
date: 2021-12-19 17:43:39 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["aop적용", "proxy", "프록시"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Boot-%EC%9E%85%EB%AC%B823-AOP%EC%A0%81%EC%9A%A9-Proxy%ED%94%84%EB%A1%9D%EC%8B%9C"
---
### **AOP적용 + Proxy(프록시)** 

* * *

**기존에 메서드가 걸리는 시간을 작성했던 방법은, 각 기능에 시간 측정 로직을 추가해서 하나하나 확인해봤다.** 

**하지만, 기능이 100개 1000개면 하나하나 추가하다가는 시간이 너무나도 많이 소모될 것이다.**

**기존**

![](/assets/img/posts/42/1.png)

 **그래서 AOP를 사용해 '원하는 곳'에 '공통 관심 사항'을 적용 시키는 방법을 사용해보자.**

**AOP 적용**

![](/assets/img/posts/42/2.png)

#### **1\. AOP 패키지 및 클래스 생성**

```java
package hello.hellospring.aop;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Aspect // AOP임을 알려주는 '어노테이션'
@Component // 빈으로 등록
public class TimeTraceAop {
	
	@Around("execution(* hello.hellospring..*(..))")
	public Object execute(ProceedingJoinPoint joinPoint) throws Throwable{
		long start = System.currentTimeMillis();
		System.out.println("START : " + joinPoint.toString()); 
		
		try {
			return joinPoint.proceed(); // proceed() 
		} finally {
			long finish = System.currentTimeMillis();
			long timeMs = finish - start;
			System.out.println("END : " + joinPoint.toString() + " " + timeMs + "ms");
		}
	}
}
```

**@Aspect : AOP임을 알려주는 '어노테이션'**

**@Component : 'Bean'으로 등록하는 '어노테이션'**

**@Around : AOP를 적용하는 경로를 나타내는 '어노테이션'**

**proceed() : 다음 메소드로 진행시켜준다. (프록시가 끝나면, 진짜 호출된 메소드를 호출시키는 기능)**

**( ※ toString()은 어떤 메소드가 실행되었는지 파악하려고 넣었다.)**

* * *

#### **Bean으로 등록 방법**

**1\. @Component '어노테이션' 사용**

**2\. SpringConfig에 'Bean'으로 등록**

**'AOP'는 정형화해서 사용하는 방법이 아니기 때문에, 봤을 때 딱 알아볼 수 있도록 '2번'을 선호한다고 한다.**

**하지만 필자는 1번으로 진행했다.**

![](/assets/img/posts/42/3.png)

*SpringConfig에 등록*

![](/assets/img/posts/42/4.png)

*@Component 통해서 등록*

* * *

#### **2\. 결과 확인**

**서버를 재실행을 시켜보면 아래처럼 AOP가 적용되는 것을 확인할 수 있다.**

![](/assets/img/posts/42/5.png)

*시작하자마자 뭔가 뜬다*

**'회원 목록'을 클릭하면 아래처럼 'Controller->Service->Repository' 순서로 실행되는 것을 볼 수 있고 걸리는 시간까지 볼 수 있다.**

![](/assets/img/posts/42/6.png)

**AOP를 사용하면 어디서 '병목현상'이 일어나는지 확인할 수 있기 때문에 퍼포먼스적인 부분에서 많은 도움이 될 것 같다.**

#### **해결**

**1\. 회원가입, 회원 조회 등 '핵심 관심 사항'과 시간을 측정하는 '공통 관심 사항' 분리**

**2\. 시간을 측정하는 로직을 별도의 공통 로직으로 만들었다.**

**3\. '핵심 관심 사항'을 깔끔하게 유지할 수 있다.**

**4\. 변경이 필요하면 이 로직만 변경하면 된다. (유지보수 용이)**

**5\. 원하는 적용 대상을 선택할 수 있다.**

#### **동작원리**

![](/assets/img/posts/42/7.png)

*AOP 적용 전*

![](/assets/img/posts/42/8.png)![](/assets/img/posts/42/9.png)

*AOP 적용 후*

**@Around를 통해서 어디에 지정할지를 정하면 스프링이 '프록시'를 먼저 만들어서 실행시킨다.**

**그리고 프록시가 실제 실행되어야 할 Service를 실행시키는 구조다.**

**이를 '프록시 방식의 AOP'라고도 불린다고 한다.**

* * *

**조금 더 자세하게 돌아가는 걸 보고 싶다면, Controller에 'memberService.getClass()'를 추가해서 콘솔로 확인해보면 알 수 있다. ( + 서버 리셋해야 한다.)**

**![](/assets/img/posts/42/10.png)**

**아래처럼 MemberService가 호출되는 게 아니라, $$EnhanceBySpring이라고 '프록시'가 먼저 생성되는 것을 볼 수 있다.**

![](/assets/img/posts/42/11.png)

* * *

**+++++**

**프록시(Proxy)**

**: \[대신\]이라는 의미를 가지고 있습니다. 프로토콜에 있어서 대리 응답 등에서 사용하는 개념이라고 할 수 있습니다.**

**보안상의 문제로 직접 통신을 주고받을 수 없는 사이에서 프록시를 이용해서 중계를 하는 개념이라고 볼 수 있습니다.**

**이렇게 중계 기능을 하는 것을 \[프록시 서버\]라고 부릅니다.**
