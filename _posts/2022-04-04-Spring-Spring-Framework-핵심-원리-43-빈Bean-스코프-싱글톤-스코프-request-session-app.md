---
title: "[Spring] Spring Framework - 핵심 원리 (43) - 빈(Bean) 스코프 + 싱글톤 스코프 + request, session, application (웹 관련 스코프) + 프로토타입 스코프"
date: 2022-04-04 22:34:08 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["application", "request", "session", "빈 스코프", "빈(bean) 스코프", "싱글톤 스코프", "웹관련 스코프", "프로토타입 빈 스코프 특징", "프로토타입 스코프"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-43-%EB%B9%88Bean-%EC%8A%A4%EC%BD%94%ED%94%84-%EC%8B%B1%EA%B8%80%ED%86%A4-%EC%8A%A4%EC%BD%94%ED%94%84-request-session-application-%EC%9B%B9-%EA%B4%80%EB%A0%A8-%EC%8A%A4%EC%BD%94%ED%94%84-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-%EC%8A%A4%EC%BD%94%ED%94%84"
---
### **빈(Bean) 스코프 + 싱글톤 스코프 + request, session, application (웹 관련 스코프) + 프로토타입 스코프**

* * *

#### **빈(Bean) 스코프란?**

**지금까지 우리는 스프링 빈이 스프링 컨테이너의 시작과 함께 생성되어서 스프링 컨테이너가 종료될 때까지 유지된다고 학습했다. 이것은 스프링 빈이 기본적으로 싱글톤 스코프로 생성되기 때문이다. 스코프는 번역 그대로 빈(Bean)이 존재할 수 있는 범위(영역)를 뜻한다.**

**스프링은 다음과 같은 다양한 스코프를 지원한다.**

**싱글톤 : 기본 스코프, 스프링 컨테이너의 시작과 종료까지 유지되는 가장 넓은 범위의 스코프 (가장 긴 스코프)**

**프로토 타입 : 스프링 컨테이너는 프로토타입의 빈(Bean)의 생성과 의존관계 주입까지만 관여하고 더는 관리하는 않는 매우 짧은 범위의 스코프 (종료 메서드가 호출이 되지 않음)**

**웹 관련 스코프 :**  
**1) request : 웹 요청이 들어오고 나갈 때까지 유지되는 스코프**  
**2) session : 웹 세션이 생성되고 종료될 때까지 유지되는 스코프**

**3) application : 웹의 서블릿 컨텍스트와 같은 범위로 유지되는 스코프**

#### **스코프 등록 방법**

#### **1) 컴포넌트 스캔 자동 등록**

```java
@Scope("prototype") 
@Component
public class HelloBean {
}
```

#### **2) 수동 등록**

```java
@Scope("prototype") 
@Bean
PrototypeBean HelloBean() {
return new HelloBean();
}
```

**지금까지는 싱글톤 스코프를 계속 사용해보았으니, 프로토 타입 스코프부터 확인해보자.**

* * *

#### **프로토타입 스코프**

**싱글톤 스코프의 빈(Bean)을 조회하면 스프링 컨테이너는 항상 같은 인스턴스의 스프링 빈을 반환한다.**

**반면에, 프로토타입 스코프를 스프링 컨테이너에 조회하면 스프링 컨테이너는 항상 새로운 인스턴스를 생성해서 반환한다.**

**싱글톤 빈 요청**

![](/assets/img/posts/120/1.png)

**1\. 싱글톤 스코프의 빈(Bean)을 스프링 컨테이너에 요청한다.**

**2\. 스프링 컨테이너는 본인이 관리하는 스프링 빈(Bean)을 반환한다.**

**3\. 이후에 스프링 컨테이너에 같은 요청이 와도 같은 객체 인스턴스의 스프링 빈을 반환한다.**

**프토토타입 빈 요청 1**

![](/assets/img/posts/120/2.png)

**1\. 프로토타입 스코프의 빈을 스프링 컨테이너에 요청한다.**

**2\. 스프링 컨테이너는 이 시점(요청이 온 시점)에 프로토타입 빈(Bean)을 생성하고, 필요한 의존관계를 주입한다.**

**프로토타입 빈 요청 2**

![](/assets/img/posts/120/3.png)

**3\. 스프링 컨테이너는 생성한 프로토타입 빈(Bean)을 클라이언트에 반환한다.**

**4\. 이후에 스프링 컨테이너에 같은 요청이 오면 항상 새로운 프로토타입 빈(Bean)을 생성해서 반환한다.**

#### **정리**

**여기서 핵심은 스프링 컨테이너는 프로토타입 빈(Bean)을 생성하고, 의존관계 주입, 초기화까지만 처리한다는 것이다.** **클라이언트에 빈(Bean)을 반환하고, 이후 스프링 컨테이너는 생성된 프로토타입 빈(Bean)을 관리하지 않는다.** **프로토타입 빈(Bean)을 관리할 책임은 프로토타입 빈(Bean)을 받은 클라이언트에 있다.**

**그래서 @PreDestroy 같은 종료 메서드가 호출되지 않는다.**

* * *

#### **코드로 확인해보자.**

#### **1\. 싱글톤 스코프**

```java
public class SingletonTest {

	@Test
	void singletonBeanFind() {
		 AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(SingletonBean.class);
		 SingletonBean singletonBean1= ac.getBean(SingletonBean.class);
		 SingletonBean singletonBean2= ac.getBean(SingletonBean.class);
		 System.out.println("singletonBean1 = " + singletonBean1 );
		 System.out.println("singletonBean2 = " + singletonBean2 );
		 Assertions.assertThat(singletonBean1).isSameAs(singletonBean2);
		 
		 ac.close();
	}
	
	@Scope("singleton")
	static class SingletonBean{
		@PostConstruct
		public void init() {
			System.out.println("SingletonBean.init");
		}
		
		@PreDestroy
		public void destroy() {
			System.out.println("SingletonBean.destroy");
		}
	}
}
```

![](/assets/img/posts/120/4.png)

```java
SingletonBean.init
singletonBean1 = hello.core.scope.SingletonTest$SingletonBean@1f3f02ee
singletonBean2 = hello.core.scope.SingletonTest$SingletonBean@1f3f02ee
22:04:45.015 [main] DEBUG org.springframework.context.annotation.AnnotationConfigApplicationContext - Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@28261e8e, started on Mon Apr 04 22:04:44 KST 2022
SingletonBean.destroy
```

**빈(Bean) 초기화 메서드를 실행하고, 같은 인스턴스의 빈을 조회하고, 종료 메서드까지 정상 호출된 것을 확인할 수 있다.** **당연한 결과이다. 싱글톤 스코프는 동일한 인스턴스를 반환한다.**

* * *

#### **2\. 프로토타입 스코프 빈** 

**참고 :  'AnnotationConfigApplicationContext'에 직접 지정해주면 '@Component'를 사용하지 않아도 된다. 대상 자체를 등록해버린다.**

```java
public class PrototypeTest {

	@Test
	void prototypeBeanFind() {
		 AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(ProtoTypeBean.class);
		 System.out.println("protoTypeBean1 " );
		 ProtoTypeBean protoTypeBean1= ac.getBean(ProtoTypeBean.class);
		 System.out.println("protoTypeBean2 " );
		 ProtoTypeBean protoTypeBean2= ac.getBean(ProtoTypeBean.class);
		 
		 System.out.println("protoTypeBean1 = " + protoTypeBean1);
		 System.out.println("protoTypeBean2 = " + protoTypeBean2);
		 
		 Assertions.assertThat(protoTypeBean1).isNotSameAs(protoTypeBean2);
		 
		 ac.close();
	}
	
	@Scope("prototype")
	static class ProtoTypeBean{
		@PostConstruct
		public void init() {
			System.out.println("prototypeBeanFind.init");
		}
		
		@PreDestroy
		public void destroy() {
			System.out.println("prototypeBeanFind.destroy");
		}
	}
}
```

**실행결과**

![](/assets/img/posts/120/5.png)

```java
protoTypeBean1 
prototypeBeanFind.init
protoTypeBean2 
prototypeBeanFind.init
protoTypeBean1 = hello.core.scope.PrototypeTest$ProtoTypeBean@1f3f02ee
protoTypeBean2 = hello.core.scope.PrototypeTest$ProtoTypeBean@1fde5d22
22:14:32.052 [main] DEBUG org.springframework.context.annotation.AnnotationConfigApplicationContext - Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@28261e8e, started on Mon Apr 04 22:14:31 KST 2022
```

#### **정리**

-   **싱글톤 빈(Bean)은 스프링 컨테이너 생성 시점에 초기화 메서드가 실행되지만, 프로토타입 스코프의 빈(Bean)은 스프링 컨테이너에서 빈을 조회할 때 생성되고, 초기화 메서드도 실행된다.**
-   **프로토타입 빈(Bean)을 2번 조회했으므로 완전히 다른 스프링 빈(Bean)이 생성되고, 초기화도 2버 실행된 것을 확인할 수 있다.**
-   **싱글톤 빈(Bean)은 스프링 컨테이너가 관리하기 때문에 스프링 컨테이너가 종료될 때의 빈의 종료 메서드가 실행되지만, 프로토타입 빈(Bean)은 스프링 컨테이너가 생성과 의존관계 주입 그리고 초기화까지만 관여하고 더 이상 관리하지 않는다.**
-   **따라서 프로토타입 빈(Bean)은 스프링 컨테이너가 종료될 때 @PreDestroy 같은 종료 메서드가 전혀 실행되지 않는다.**

* * *

#### **프로토타입 빈의 특징 정리**

**1\. 스프링 컨테이너에 요청할 때마다 새로 생성된다.**

**2\. 스프링 컨테이너는 프로토타입 빈의 생성과 의존관계 주입 그리고 초기화까지만 관여한다.**

**3\. 종료 메서드가 호출되지 않는다.  
(만약 호출을 원한다면, protoTypeBean1.destroy(); 이런 식으로 직접 닫아야 한다.)**

**4\. 그래서 프로토타입 빈은 프로토타입 빈을 조회한 클라이언트가 관리해야 한다.  
종료 메서드에 대한 호출도 클라이언트가 직접 해야 한다.**
