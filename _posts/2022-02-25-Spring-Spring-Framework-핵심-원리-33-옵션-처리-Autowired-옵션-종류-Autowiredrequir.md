---
title: "[Spring] Spring Framework - 핵심 원리 (33) - 옵션 처리, Autowired 옵션 종류, @Autowired(required=false), @Nullable, Optional<T>"
date: 2022-02-25 23:52:42 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["@nullable", "autowired", "autowired 옵션", "autowired 종류", "optional", "required = false"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-33-%EC%98%B5%EC%85%98-%EC%B2%98%EB%A6%AC-Autowired-%EC%98%B5%EC%85%98-%EC%A2%85%EB%A5%98-Autowiredrequiredfalse-Nullable-OptionalT"
---
### **옵션 처리, Autowired 옵션 종류, @Autowired(required=false), @Nullable, Optional<T>**

* * *

#### **옵션 처리**

**주입할 스프링 빈이 없어도 동작해야 할 때가 있다.**

**그런데 @Autowired만 사용하면 required 옵션의 기본값이 true로 되어있어서 자동 주입 대상이 없으면**

**오류가 발생한다.**

**자동 주입 대상을 옵션으로 처리하는 방법은 다음과 같다.**

1.  **@Autowired(required=false) : 자동 주입할 대상이 없으면 수정자 메서드 자체가 호출 안됨**
2.  **org.springframework.lang.@Nullable : 자동 주입할 대상이 없으면 null이 입력된다.**
3.  **Optional <> : 자동 주입할 대상이 없으면 Optional.empty가 입력된다.**

> **Optional<T>는 null이 올 수 있는 값을 감싸는 Wrapper 클래스로, 참조하더라도 NPE(NullPointException)dl 발생하지 않도록 도와준다.**

**코드로 알아보자.**

**먼저, Member는 스프링 빈에 등록이 되어있지 않은 상태다. 당연히 못 찾아오는 게 정상이다.**

**아래 3개의 테스트 중에서 1번에서 에러가 발생할 것이다.**

```java
public class AutowiredTest {
	
	@Test
	public void AutowiredOption() {
		ApplicationContext ac = new AnnotationConfigApplicationContext(TestBean.class);
	}

	static class TestBean{
		
		@Autowired(required = false)
		public void setNoBean1(Member member) { 
			System.out.println("noBean1 = " + member);
		}
		@Autowired
		public void setNoBean2(@Nullable Member member) {
			System.out.println("noBean2 = " + member);
		}
		@Autowired(required = false)
		public void setNoBean3(Optional<Member> member) {
			System.out.println("noBean3 = " + member);
		}
	}
}
```

**아래 결과에서도 못 찾는다고 나온다.**

![](/assets/img/posts/103/1.png)

**@Autowired(required = false)로 변경해서 다시 돌려보자.**

```java
		@Autowired(required = false)
		public void setNoBean1(Member noBean1) {
			System.out.println("noBean1 = " + noBean1);
		}
```

**문제없이 잘 돌아간다.**

![](/assets/img/posts/103/2.png)

**콘솔 결과를 보면 아래처럼 출력된다.**

**noBean1은 없으니까 아예 출력이 안 되는 것이고, noBean2는 없으면 null값을 들어가게끔 옵션을 설정해놔서 아래처럼 결과가 나오는 것이다. noBean3는 Optional.empty가 발생한다.**

```java
noBean2 = null
23:47:07.830 [main] WARN org.springframework.context.annotation.AnnotationConfigApplicationContext - Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'autowiredTest.TestBean': Unsatisfied dependency expressed through method 'setNoBean3' parameter 0; nested exception is org.springframework.beans.factory.NoSuchBeanDefinitionException: No qualifying bean of type 'net.bytebuddy.dynamic.DynamicType$Builder$FieldDefinition$Optional<hello.core.member.Member>' available: expected at least 1 bean which qualifies as autowire candidate. Dependency annotations: {}
```
