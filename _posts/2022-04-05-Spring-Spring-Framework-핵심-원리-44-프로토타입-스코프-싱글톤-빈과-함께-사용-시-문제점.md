---
title: "[Spring] Spring Framework - 핵심 원리 (44) - 프로토타입 스코프 - 싱글톤 빈과 함께 사용 시, 문제점"
date: 2022-04-05 22:57:09 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["싱글톤 빈", "싱글톤 프로토타입 빈 동시 사용", "싱글톤 프로토타입 빈 동시 사용시 문제점", "싱글톤 프로토타입 빈 차이", "프로토타입 스코프 빈"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-44-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-%EC%8A%A4%EC%BD%94%ED%94%84-%EC%8B%B1%EA%B8%80%ED%86%A4-%EB%B9%88%EA%B3%BC-%ED%95%A8%EA%BB%98-%EC%82%AC%EC%9A%A9-%EC%8B%9C-%EB%AC%B8%EC%A0%9C%EC%A0%90"
---
**프로토타입 스코프 - 싱글톤 빈과 함께 사용 시, 문제점**

* * *

#### **프로토타입 스코프 - 싱글톤 빈(Bean)과 함께 사용 시 문제점**

**스프링 컨테이너에 프로토타입 스코프의 빈(Bean)을 요청하면 항상 새로운 객체 인스턴스를 생성해서 반환한다.** **하지만, 싱글톤 빈(Bean)과 함께 사용할 때는 의도한 대로 잘 동작하지 않으므로 주의해야 한다.**

**그림과 코드로 알아보자.**

**먼저 스프링 컨테이너에 프로토타입 빈(Bean)을 직접 요청하는 예제를 보자.**

#### **스프링 컨테이너에 프로토타입 빈(Bean) 직접 요청 1**

![](/assets/img/posts/121/1.png)

**1\. 클라이언트 A는 스프링 컨테이너에 프로토타입 빈(Bean)을 요청한다.**

**2\. 스프링 컨테이너는 프로토타입 빈(Bean)을 새로 생성해서 반환(x01)한다. 해당 빈의 count 필드 값은 0이다.**

**3\. 클라이언트는 조회한 프로토타입 빈(Bean)에 addCount()를 호출하면서 count 필드를 +1 한다.**

**결과적으로 프로토타입 빈(Bean)-x01의 count는 1이 된다.**

#### **스프링 컨테이너에 프로토타입 빈(Bean) 직접 요청 2**

![](/assets/img/posts/121/2.png)

**1\. 클라이언트 B는  스프링 컨테이너에 프로토타입 빈(Bean)을 요청한다.**

**2\. 스프링 컨테이너는 프로토타입 빈(Bean)을 새로 생성해서 반환(x02)한다. 해당 빈(Bean)의 count 필드 값은 0이다.**

**3\. 클라이언트는 조회한 프로토타입 빈(Bean)에 addCount()를 호출하면서 count필드를 +1 한다.**

**결과적으로 프로토타입 빈(x02)의 count는 1이 된다.**

**코드로 확인해보자.**

```java
public class SingletonWithProtoTypeTest1 {

	@Test
	void prototypeFind() {
		 AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(PrototypeBean.class);
		 PrototypeBean prototypeBean1= ac.getBean(PrototypeBean.class);
		 prototypeBean1.addCount();
		 Assertions.assertThat(prototypeBean1.getCount()).isEqualTo(1);
		 
		 PrototypeBean prototypeBean2= ac.getBean(PrototypeBean.class);
		 prototypeBean2.addCount();
		 Assertions.assertThat(prototypeBean2.getCount()).isEqualTo(1);
		 
		 ac.close();
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

![](/assets/img/posts/121/3.png)

```java
PrototypeBean.init = hello.core.scope.SingletonWithProtoTypeTest1$PrototypeBean@53de625d
PrototypeBean.init = hello.core.scope.SingletonWithProtoTypeTest1$PrototypeBean@305a0c5f
```

**사실 굳이 안 해봐도 되는 과정이지만, 다음 내용을 설명하려고 했다.**

* * *

**이번에는 clientBean이라는 싱글톤 빈(Bean)이 의존관계 주입을 통해서 프로토타입 빈(Bean)을 주입받아서 사용하는 예를 보자.**

#### **싱글톤에서 프로토타입 빈(Bean) 사용 1**

![](/assets/img/posts/121/4.png)

**clientBean은 싱글톤이므로, 보통 스프링 컨테이너 생성 시점에 함께 생성되고, 의존관계 주입도 발생한다.**

**1\. clientBean은 의존관계 자동 주입을 사용한다. 주입 시점에 스프링 컨테이너에 프로토타입 빈(Bean)을 요청한다.**

**2\. 스프링 컨테이너는 프로토타입 빈(Bean)을 생성해서 clientBean에 반환한다. 프로토타입 빈(Bean)의 count 필드 값은 0이다.**

**이제 clientBean은 프로토타입 빈(Bean)을 내부 필드에 보관한다. (정확히는 참조값을 보관한다.)**

#### **싱글톤에서 프로토타입 빈 사용 2**

![](/assets/img/posts/121/5.png)

**클라이언트 A는 clientBean을 스프링 컨테이너에 요청해서 받는다. 싱글톤이므로 항상 같은 clientBean이 반환된다.**

**3\. 클라이언트 A는 clientBean.logic()을 호출한다.**

**4\. clientBean은 prototypeBean의 addCount()를 호출해서 프로토타입 빈(Bean)의 count를 증가한다. count값이 1이 된다.**

#### **싱글톤에서 프로토타입 빈 사용 3**

![](/assets/img/posts/121/6.png)

**클라이언트 B는 clientBean을 스프링 컨테이너에 요청해서 받는다. 싱글톤이므로 항상 같은 clientBean이 반환된다.**

**여기서 중요한 점이 있는데, clientBean이 내부에 가지고 있는 프로토타입 빈(Bean)은 이미 과거에 주입이 끝난 빈(Bean)이다. 주입 시점에 스프링 컨테이너에 요청해서 프로토타입 빈(Bean)이 새로 생성이 된 것이지,  
사용할 때마다 새로 생성되는 것이 아니다!**

**5\. 클라이언트 B는 clientBean.logic()을 호출한다.**

**6\. clientBean은 prototypeBean의 addCount()를 호출해서 프로토타입 빈(Bean)의 count를 증가한다. 원래 count값이 1이었으므로, 2가 된다.**

**코드로 확인해보자.**

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
		 Assertions.assertThat(count2).isEqualTo(2);
		 
		 ac.close();
	}
	
	@Scope("singleton")
	static class ClientBean{
		private final PrototypeBean prototypeBean; // 생성시점에 주입
		
		@Autowired
		public ClientBean(PrototypeBean prototypeBean) {
			this.prototypeBean = prototypeBean;
		}
		
		public int logic() {
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

![](/assets/img/posts/121/7.png)

**스프링은 일반적으로 싱글톤 빈을 사용하므로, 싱글톤 빈이 프로토타입 빈(Bean)을 사용하게 된다.  
그런데 싱글톤 빈(Bean)은 생성 시점에만 의존관계 주입을 받기 때문에, 프로토타입 빈(Bean)이 새로 생성되기는 하지만, 싱글톤 빈(Bean)과 함께 계속 유지되는 것이 문제다.**

**아마 원하는 것이 이런 것은 아닐 것이다. 프로토타입 빈을 주입 시점에만 새로 생성하는 게 아니라, 사용할 때마다 새로 생성해서 사용하는 것을 원할 것이다.**

> **참고 : 여러 빈(Bean)에서 같은 프로토타입 빈(Bean)을 주입받으면, 주입받는 시점에 각각 새로운 프로토타입 빈(Bean)이 생성된다. 예를 들어서, clientA, clientB가 각각 의존관계 주입을 받으면 각각 다른 인스턴스의 프로토타입 빈(Bean)을 주입받는다.**  
> **clientA -> prototypeBeanx01**  
> **clientB -> prototypeBeanx02**  
> **물론 사용할 때마다 새로 생성되는 것은 아니다.**
