---
title: "[Spring] Spring Framework - 핵심 원리 (25) - 싱글톤 방식의 주의점"
date: 2022-02-09 23:50:44 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["멀티스레드 문제", "무상태(stateless)", "싱글톤 방식의 문제점", "싱글톤 방식의 주의점", "유지(stateful)"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-25-%EC%8B%B1%EA%B8%80%ED%86%A4-%EB%B0%A9%EC%8B%9D%EC%9D%98-%EC%A3%BC%EC%9D%98%EC%A0%90"
---
### **싱글톤 방식의 주의점, 문제점 + 무상태(stateless), 상태 유지(stateful) + 공유 필드**

* * *

#### **싱글톤 방식의 주의점**

-   **싱글톤 패턴이든, 스프링 같은 싱글톤 컨테이너를 사용하든, 객체 인스턴스를 하나만 생성해서 공유하는 싱글톤 방식은 여러 클라이언트가 하나의 같은 객체 인스턴스를 공유하기 때문에 싱글톤 객체는 상태를 유지(stateful)하게 설계하면 안 된다.**
-   **무상태(stateless)로 설계해야 한다.**

1.   **특정 클라이언트에 의존적인 필드가 있으면 안 된다.**
2.   **특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안 된다.**
3.   **가급적 읽기만 가능해야 한다.**
4.   **필드 대신에 자바에서 공유되지 않는, 지역변수, 파라미터, TreadLocal 등을 사용해야 한다.**

-   **스프링 빈의 필드에 공유 값을 설정하면 정말 큰 장애가 발생할 수 있다.**

**코드를 통해서 예시를 한번 확인해보자. 먼저 StatefulService를 만들자.**

```java
public class StatefulService {

	private int price; //상태를 유지하는 필드
	
	public void order(String name, int price) {
		System.out.println("name =" + name + "price =" + price);
		this.price = price; //여기가 문제
	}
	
	public int getPrice() {
		return price;
	}
}
```

  
**상황 : A사용자가 10000원을 주문하고, 금액을 조회하는 중간에 B사용자가 중간에 끼어들어서 10000원을 주문하는 상황.  그렇다면 price는?**

```java
public class StatufulServiceTest {
	
	@Test
	void statefulServiceSingleton() {
		AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);
		StatefulService statefulService1 = ac.getBean(StatefulService.class);
		StatefulService statefulService2 = ac.getBean(StatefulService.class);
		
		//ThreadA : A 사용자 10000원 주문
		statefulService1.order("userA", 10000);
		//ThreadA : B 사용자 10000원 주문
		statefulService2.order("userA", 20000);
		
		//ThreadA : A 사용자 주문 금액 조회
		int price = statefulService1.getPrice();
		System.out.println("price =" + price);		
        
        Assertions.assertThat(statefulService1.getPrice()).isEqualTo(20000);
	}
	static class TestConfig {
		@Bean
		public StatefulService statefulService() {
			return new StatefulService();
		}
	}
}
```

**결과는 20000원이 리턴된다. 그리고 검증까지 했을 때, 초록불이 뜬다. 이건 망한 서비스가 된다.**

```java
name =userAprice =10000
name =userAprice =20000
price =20000
```

![](/assets/img/posts/89/1.png)

**왜냐하면, 같은 객체를 사용하고 있기 때문에,**

**(웹에서 요청이 오면, 각각 Thread가 할당이 되지만 같은 객체를 사용하고 있다.)**

**statefulService1에서 '10000원'을 주문하면, StatefulService의 'price'에 '10000원'이 되지만,**

**바로 statefulService2가 '20000원'을 주문하게 되면  'price'가 '20000원'이 된다.**

* * *

#### **정리**

-   **ThreadA가 사용자 A 코드를 호출하고, ThreadB가 사용자 B 코드를 호출한다 가정하자.**
-   **StatefulService의 price 필드는 공유되는 필드인데, 특정 클라이언트가 값을 변경한다.**
-   **사용자 A의 주문금액은 10000원이 되어야 하는데, 20000원이라는 결과가 나왔다.**
-   **실무에서 이런 경우를 종종 보는데, 이로 인해 정말 해결하기 어려운 큰 문제들이 터진다.**
-   **진짜 공유 필드는 조심해야 한다!. 스프링 빈은 항상 무상태(Stateless)로 설계하자!**

* * *

**간단하게, price를 지역변수로 사용해서 문제를 해결해보자.** 

**지역변수는 공유가 안되기 때문에, 원하는 값을 얻을 수 있다.**

```java
package hello.core.singleton;

public class StatefulService {

	public int order(String name, int price) { // void --> int
		System.out.println("name =" + name + "price =" + price);
		return price; // --> 지역변수로 사용
	}
}
```

```java
public class StatufulServiceTest {
	
	@Test
	void statefulServiceSingleton() {
		AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);
		StatefulService statefulService1 = ac.getBean(StatefulService.class);
		StatefulService statefulService2 = ac.getBean(StatefulService.class);
		
		//ThreadA : A 사용자 10000원 주문
		int userAPrice = statefulService1.order("userA", 10000);
		//ThreadA : B 사용자 10000원 주문
		int userBPrice = statefulService2.order("userV", 20000);
		
		//ThreadA : A 사용자 주문 금액 조회
		System.out.println("price =" + userAPrice);

	}
	static class TestConfig {
		@Bean
		public StatefulService statefulService() {
			return new StatefulService();
		}
	}
}
```

```java
name =userAprice =10000
name =userVprice =20000
price =10000
```

**멀티스레드(Multi Thread) 문제이기 때문에, 실무에서도 많이 발생할 수 있는 문제라고 한다.**

**꼭 잘 정리하자.**
