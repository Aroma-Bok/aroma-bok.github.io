---
title: "[Spring] Spring Framework - 핵심 원리 (31) - 빈 중복 등록+ 빈 충돌 + 자동 빈 등록 vs 자동 빈 등록 + 수동 빈 등록 vs 자동 빈 등록 +  ConflictingBeanDefinitionException"
date: 2022-02-23 21:14:27 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["conflictingbeandefinitionexception", "spring.main.allow-bean-definition-overriding=true", "빈 자동등록", "빈 중복등록", "빈 중복등록 오류", "빈 충돌", "수동 빈 등록 vs 자동 빈 등록"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-31-%EB%B9%88-%EC%A4%91%EB%B3%B5-%EB%93%B1%EB%A1%9D-%EB%B9%88-%EC%B6%A9%EB%8F%8C-%EC%9E%90%EB%8F%99-%EB%B9%88-%EB%93%B1%EB%A1%9D-vs-%EC%9E%90%EB%8F%99-%EB%B9%88-%EB%93%B1%EB%A1%9D-%EC%88%98%EB%8F%99-%EB%B9%88-%EB%93%B1%EB%A1%9D-vs-%EC%9E%90%EB%8F%99-%EB%B9%88-%EB%93%B1%EB%A1%9D-ConflictingBeanDefinitionException"
---
### 빈 중복 등록+ 빈 충돌 + 자동 빈 등록 vs 자동 빈 등록 + 수동 빈 등록 vs 자동 빈 등록 +  ConflictingBeanDefinitionException

* * *

**컴포넌트 스캔에서 같은 빈 이름을 등록하면 어떻게 될까?**

**다음 두 가지 상황이 있다.**

**1\. 자동 빈 등록 vs 자동 빈 등록**

**2\. 수동 빈 등록 vs 자동 빈 등록**

#### **자동 빈 등록 vs 자동 빈 등록**

**컴포넌트 스캔에 의해 자동으로 스프링 빈이 등록되는데, 그 이름이 같은 경우 스프링은 오류를 발생시킨다.**  
**\-> ConflictingBeanDefinitionException 예외 발생**

**코드로 알아보자.**

**1\. OrderServiceImpl의 @Component명 변경**

```java
@Component("service")
public class OrderServiceImpl implements OrderService
```

**2\. MemberServiceImpl의 @Component명 변경**

```java
@Component("service")
public class MemberServiceImpl implements MemberService
```

**3\. AutoAppConfigTest로 테스트 실행**

**결과는 아래처럼 친절하게(?) ConflictingBeanDefinitionException 예외를 발생시켜서 원인을 말해준다.**

```java
org.springframework.beans.factory.BeanDefinitionStoreException: Failed to parse configuration class [hello.core.AutoAppConfig]; nested exception is org.springframework.context.annotation.ConflictingBeanDefinitionException: 
Annotation-specified bean name 'service' for bean class [hello.core.order.OrderServiceImpl] conflicts with existing, non-compatible bean definition of same name and class [hello.core.member.MemberServiceImpl]
```

#### **수동 빈 등록 vs 자동 빈 등록**

**만약 수동 빈 등록과 자동 빈 등록에서 빈 이름이 충돌되면 어떻게 될까?**

**1\. MemoryMemberRepository와 똑같은 Bean을 수동으로 등록**

```java
	@Bean(name = "memoryMemberRepository")
	MemberRepository memberRepository() {
		return new MemoryMemberRepository();
	}
}
```

**2\. AutoAppConfig 테스트** 

**결과를 확인하면 Overriding bean definition로 시작하는 내용이 나온다. 그리고 테스트도 통과한다.**

```java
 Overriding bean definition for bean 'memoryMemberRepository' with a different definition: replacing [Generic bean: class [hello.core.member.MemoryMemberRepository]; 
 scope=singleton; abstract=false; lazyInit=null; autowireMode=0; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=null; factoryMethodName=null; 
 initMethodName=null; destroyMethodName=null; defined in file [D:\EZEN08\MySpring Framework\core\bin\main\hello\core\member\MemoryMemberRepository.class]] with [Root bean: class [null]; scope=; abstract=false; lazyInit=null; autowireMode=3; 
 dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=autoAppConfig; factoryMethodName=memberRepository; initMethodName=null; destroyMethodName=(inferred); defined in hello.core.AutoAppConfig]
```

![](/assets/img/posts/100/1.png)

*수동인 경우에는 통과!*

**이 경우, 수동 빈 등록이 우선권을 가진다! (수동 빈이 자동 빈을 오버 라이딩해버린다.)**

> **참고 (중요)**  
> **개발자가 의도적으로 설정해서 이런 결과가 만들어지기보다는 여러 설정들이 꼬여서 이런 결가가 만들어지는 경우가  
> 대부분이다! 그러면 정말 잡기 어려운 버그가 만들어진다. 그래서 최근 스프링 부트에서는 수동 빈 등록과 자동 빈 등록이 충돌 나면 오류가 발생하도록 기본 값을 바꾸었다.(즉, 기본값이 False!!!)**

**CoreApplication을 실행하면 아래처럼 오류가 발생한다.**

```java
Description:

The bean 'memoryMemberRepository', defined in class path resource [hello/core/AutoAppConfig.class], could not be registered. A bean with that name has already been defined in file [D:\EZEN08\MySpring Framework\core\bin\main\hello\core\member\MemoryMemberRepository.class] and overriding is disabled.

Action:

Consider renaming one of the beans or enabling overriding by setting spring.main.allow-bean-definition-overriding=true
```

**만약에 오버 라이딩하고 싶으면 application.properties에 콘솔에서 가르치고 있는 내용인**

**spring.main.allow-bean-definition-overriding=true을 등록하면 스프링 부트로 실행해도 오류가 발생하지 않는다.** 

```java
spring.main.allow-bean-definition-overriding=true
```

```java
2022-02-23 21:06:48.903  INFO 14268 --- [           main] hello.core.CoreApplication               : Starting CoreApplication using Java 15.0.2 on DESKTOP-6G8OBCG with PID 14268 (D:\EZEN08\MySpring Framework\core\bin\main started by bock1 in D:\EZEN08\MySpring Framework\core)
2022-02-23 21:06:48.907  INFO 14268 --- [           main] hello.core.CoreApplication               : No active profile set, falling back to default profiles: default
call AppConfig.memberRepository
call AppConfig.memberService
call AppConfig.orderService
2022-02-23 21:06:50.142  INFO 14268 --- [           main] hello.core.CoreApplication               : Started CoreApplication in 2.111 seconds (JVM running for 3.049)
```

### **개발은 혼자 하는 게 아니기 때문에, 명확하지 않으면 하지 않는 것이 좋다.**  
**어떠한 상황이 생길지 모른다.**
