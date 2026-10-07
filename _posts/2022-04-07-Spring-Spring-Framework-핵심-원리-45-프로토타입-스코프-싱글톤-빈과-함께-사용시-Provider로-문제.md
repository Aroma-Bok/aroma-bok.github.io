---
title: "[Spring] Spring Framework - 핵심 원리 (45) - 프로토타입 스코프 - 싱글톤 빈과 함께 사용시 Provider로 문제 해결 + 의존관계 조회(탐색) + Dependency Lookup(DL) + ObjectFactory, ObjectProvider + JSR-330 Provider"
date: 2022-04-07 22:29:16 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["dependency lookup", "dl", "jsr330", "jsr330 provider", "objectfactory", "objectprovider", "의존관계 조회"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-45-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-%EC%8A%A4%EC%BD%94%ED%94%84-%EC%8B%B1%EA%B8%80%ED%86%A4-%EB%B9%88%EA%B3%BC-%ED%95%A8%EA%BB%98-%EC%82%AC%EC%9A%A9%EC%8B%9C-Provider%EB%A1%9C-%EB%AC%B8%EC%A0%9C-%ED%95%B4%EA%B2%B0-%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84-%EC%A1%B0%ED%9A%8C%ED%83%90%EC%83%89-Dependency-LookupDL-ObjectFactory-ObjectProvider-JSR-330-Provider"
---
### **프로토타입 스코프 - 싱글톤 빈과 함께 사용 시 Provider로 문제 해결 + 의존관계 조회(탐색) + Dependency Lookup(DL) + ObjectFactory, ObjectProvider + JSR-330 Provider**

* * *

**싱글톤 빈과 프로토타입 빈을 함께 사용할 때, 어떻게 하면 사용할 때마다 항상 새로운 프로토타입 빈을 생성할 수 있을까?**

#### **스프링 컨테이너에 요청**

**가장 간단한 방법은 싱글톤 빈이 프로토타입을 사용할 때마다 스프링 컨테이너에 새로 요청하는 것이다.**

**저번 시간에 작성했던 ClientBean 내용만 아래처럼 바꾸면 사용은 가능하다. (하지만 무식한 방법이다.)**

```java
static class ClientBean {

	@Autowired
	private ApplicationContext ac; public int logic() {
    
            PrototypeBean prototypeBean = ac.getBean(PrototypeBean.class);             
            prototypeBean.addCount();
			int count = prototypeBean.getCount(); 
            return count;
	}
}
```

-   **실행해보면 ac.getBean()을 통해서 항상 새로운 프로토타입 빈이 생성되는 것을 확인할 수 있다.**
-   **의존관계를 외부에서 주입(DI) 받는 게 아니라 이렇게 직접 필요한 의존관계를 찾는 것을 Dependency Lookup(DL) 의존관계 조회(탐색)이라 한다.**
-   **그런데 이렇게 스프링의 애플리케이션 컨텍스트 전체를 주입받게 되면, 스프링 컨테이너에 종속적인 코드가 되고, 단위 테스트도 어려워진다.**
-   **지금 필요한 기능은 지정한 프로토타입 빈을 컨테이너에서 대신 찾아주는 딱 DL 정도의 기능만 제공하는 무언가가 있으면 된다.**

* * *

**스프링에는 이미 모든 게 준비되어 있다.**

#### **ObjectFactory, ObjectoryProvider**

**지정한 빈을 컨테이너에서 대신 찾아주는 DL 서비스를 제공하는 것이 바로 ObjectProvider이다.**

**참고로 과거에는 ObjectFactory가 있었는데, 여기에 편의 기능을 추가해서 ObjectProvider가 만들어졌다.**

**코드를 통해서 먼저 보자. 집중해서 볼 곳은 @Scope 부분이다.**

**@Autowired**  
**private ObjectProvider <PrototypeBean> prototypeBeanProvider; 를 통해서 새로운 프로토타입 빈이 생성되는 것을 확인해보자.**

```java
public class SingletonWithProtoTypeTest1 {
	@Test
	void singletonClientUsePrototype() {
		 AnnotationConfigApplicationContext ac = 
				 new AnnotationConfigApplicationContext(ClientBean.class,PrototypeBean.class);
		 
		 ClientBean clientBean1= ac.getBean(ClientBean.class);
		 int count1 = clientBean1.logic();
		 Assertions.assertThat(count1).isEqualTo(1);
		 
		 ClientBean clientBean2= ac.getBean(ClientBean.class);
		 int count2 = clientBean2.logic();
		 Assertions.assertThat(count2).isEqualTo(1);
		 
		 ac.close();
	}
	
	@Scope("singleton")
	static class ClientBean{
		private final PrototypeBean prototypeBean; // 생성시점에 주입
		
		@Autowired
		private ObjectProvider<PrototypeBean> prototypeBeanProvider;
		
		@Autowired
		public ClientBean(PrototypeBean prototypeBean) {
			this.prototypeBean = prototypeBean;
		}
		
		public int logic() {
			PrototypeBean prototypeBean = prototypeBeanProvider.getObject();
			prototypeBean.addCount(); // 생성시점에 주입된 빈(Bean)을 사용
			int count = prototypeBean.getCount();
			return count;
		}
	}
	
	@Scope("prototype")
	static class PrototypeBean{
		
		private int count = 0;
		
		public void addCount() {
			count ++;
		}
		
		public int getCount() {
			return count;
		}
		
		@PostConstruct
		public void init() {
			System.out.println("PrototypeBean.init = " + this);
		}
		
		@PreDestroy
		public void destroy() {
			System.out.println("PrototypeBean.destroy");
		}
	}
}
```

![](/assets/img/posts/122/1.png)

```java
PrototypeBean.init = hello.core.scope.SingletonWithProtoTypeTest1$PrototypeBean@2df6226d
PrototypeBean.init = hello.core.scope.SingletonWithProtoTypeTest1$PrototypeBean@4983159f
```

-   **실행해보면 prototypeBeanProvider.getObject()를 통해서 항상 새로운 프로토타입 빈이 생성되는 것을 확인할 수 있다.**
-   **ObjectProvider의 getObject()를 호출하면 내부에서는 스프링 컨테이너를 통해 해당 빈을 찾아서 반환한다. (DL)**
-   **스프링이 제공하는 기능을 사용하지만, 기능이 단순하므로 단위 테스트를 만들거나 mock 코드를 만들기는 훨씬 쉬워진다.**
-   **ObjectProvider는 지금 딱 필요한 DL 정도의 기능만 제공한다.**

#### **특징**

**1\. ObjectFactory : 기능이 단순, 별도의 라이브러리 필요 없음, 스프링에 의존**

**2\. ObjectProider : ObjectFactory 상속, 옵션, 스트림 처리 등 편의 기능이 많고, 별도의 라이브러리 필요 없음, 스프링에 의존**

* * *

#### **JSR-330 - Provider**

**마지막 방법은 javax.inject.Provider라는 JSR-330 자바 표준을 사용하는 방법이다.**

**이 방법을 사용하려면 javax.inject:javax.inject:1 라이브러리를 gradle에 추가해야 한다.**

**build.gradle에 아래의 코드를 추가해야 한다.**

```java
implementation 'javax.inject:javax.inject:1'
```

**제대로 들어왔는지 확인해보면 아래처럼 추가해준 패키지의 Provider가 존재한다.**

![](/assets/img/posts/122/2.png)

*ctrl + shift + T*

**코드로 Provider를 적용해보자. (ClientBean 부분만 아래처럼 변경해주고 사용하면 된다.)**

```java
	@Scope("singleton")
	static class ClientBean{
		private final PrototypeBean prototypeBean; // 생성시점에 주입
		
		@Autowired
		//private ObjectProvider<PrototypeBean> prototypeBeanProvider;
		private Provider<PrototypeBean> provider;
		
		@Autowired
		public ClientBean(PrototypeBean prototypeBean) {
			this.prototypeBean = prototypeBean;
		}
		
		public int logic() {
			PrototypeBean prototypeBean = provider.get();
			prototypeBean.addCount(); // 생성시점에 주입된 빈(Bean)을 사용
			int count = prototypeBean.getCount();
			return count;
		}
	}
```

![](/assets/img/posts/122/3.png)

```java
PrototypeBean.init = hello.core.scope.SingletonWithProtoTypeTest1$PrototypeBean@a4add54
PrototypeBean.init = hello.core.scope.SingletonWithProtoTypeTest1$PrototypeBean@61a88b8c
```

-   **실행해보면 provider.get()을 통해서 항상 새로운 프로토타입 빈이 생성되는 것을 확인할 수 있다.**
-   **provider의 get()을 호출하면 내부에서는 스프링 컨테이너를 해당 빈을 찾아서 반환한다. (DL)**
-   **자바 표준이고, 기능이 단순하므로 단위 테스트를 만들거나 mock 코드를 만들기는 훨씬 쉬워진다.**
-   **Provider는 지금 딱 필요한 DL 정도의 기능만 제공한다.**

#### **특징**

**1\. get() 메서드 하나로 기능이 매우 단순하다.**

**2\. 별도의 라이브러리가 필요하다.**

**3\. 자바 표준이므로, 스프링이 아닌 다른 컨테이너에서도 사용할 수 있다.**

* * *

**정리**

-   **그러면 프로토타입 빈을 언제 사용할까? 매번 사용할 때마다 의존관계 주입이 완료된 새로운 객체가 필요하면 사용하면 된다. 그런데 실무에서 웹 애플리케이션을 개발해보면, 싱글톤 빈으로 대부분의 문제를 해결할 수 있기 때문에 프로토타입 빈을 직접적으로 사용하는 일은 매우 드물다.**
-   **ObjectProvider, JSR330 Provider 등은 프로토타입뿐만 아니라 DL이 필요한 경우는 언제든지 사용할 수 있다.**

> **참고 : 스프링이 제공하는 메서드에 @Lookup 어노테이션을 사용하는 방법도 있지만, 이전 방법들도 충분하고, 고려해야 할 내용도 많아서 생략하겠다.**

> **참고 : 실무에서 자바 표준인 JSR-330 Provider를 사용할 것인지, 아니면 스프링이 제공하는 ObjectProvider를 사용할 것인지 고민이 될 것이다. ObjectProvider는 DL을 위한 편의 기능을 많이 제공해주고 스프링 외에 별도의 의존관계 추가가 필요 없기 때문에 편리하다. 만약 코드를 스프링이 아닌 다른 컨테이너에서도 사용할 수 있어야 한다면 JSR-330 Provider를 사용해야 한다.**  
>   
> **스프링을 사용하다 보면 이 기능뿐만 아니라, 다른 기능들도 자바 표준과 스프링이 제공하는 기능이 겹칠 때가 많다.  
> 대부분 스프링이 더 다양하고 편리한 기능을 제공해주기 때문에, 특별히 다른 컨테이너를 사용할 일어 없다면,**  
> **스프링이 제공하는 기능을 사용하면 된다.**
