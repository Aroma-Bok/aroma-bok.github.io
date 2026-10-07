---
title: "[Spring] Spring Framework - 핵심 원리 (41) - 빈 생명주기 콜백(Bean LifeCycle Callback Method) + 콜백이란? + 콜백 종류 (초기화, 소멸 전 콜백) + 스프링 빈 라이프사이클"
date: 2022-03-28 23:14:41 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["bean lifecycle callback method", "빈 생명주기 콜백 함수", "빈(bean) 생명주기", "소멸 전 콜백", "스프링 빈 라이프사이클", "초기화 콜백", "커넥션 풀 (connection pool)", "콜백 종류"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-41-%EB%B9%88-%EC%83%9D%EB%AA%85%EC%A3%BC%EA%B8%B0-%EC%BD%9C%EB%B0%B1Bean-LifeCycle-Callback-Method-%EC%BD%9C%EB%B0%B1%EC%9D%B4%EB%9E%80-%EC%BD%9C%EB%B0%B1-%EC%A2%85%EB%A5%98-%EC%B4%88%EA%B8%B0%ED%99%94-%EC%86%8C%EB%A9%B8-%EC%A0%84-%EC%BD%9C%EB%B0%B1-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B9%88-%EB%9D%BC%EC%9D%B4%ED%94%84%EC%82%AC%EC%9D%B4%ED%81%B4"
---
### **빈 생명주기 콜백(Bean LifeCycle Callback Method) + 콜백이란? + 콜백 종류 (초기화, 소멸 전 콜백) + 스프링 빈 라이프사이클 + **커넥션 풀 (**Connection Pool)**  
****

* * *

**빈 생명주기 콜백 (Bean LifeCycle Callback Method)**

**: 스프링 빈이 생성되거나 소멸되기 직전에 빈(Bean) 안에 있는 메서드를 호출해주는 기능**

**콜백 (Call-Back)  
: 어떠한 이벤트가 발생했거나 특정 시점에 도달했을 때 시스템에서 호출하는 함수**

**커넥션 풀 (**Connection Pool)**  
: 일반적으로 서버는 동시에 사용할 수 있는 사람의 수라는 개념이 존재한다. 동시 접속자 수를 벗어나게 될 경우 에러(예외)가 발생하게 된다. 하지만,  Connection Pool을 이용하면 동시 접속자가 가질 수 있는 Connection을 하나로 모아놓고 관리한다는 개념이다. 즉, 누군가 접속하면 자신이 관리하는 Pool에서 남아있는 Connection을 제공한다. 만약, 남아있는 Connection이 없는 경우라면 해당 클라이언트는 대기상태로 전환되었다가 순서대로 제공한다.  
**

#### **빈 생명주기 콜백 시작 - Bean LifeCycle Callback Method**

**데이터베이스 커넥션 풀이나, 네트워크 소켓처럼 애플리케이션 시작 시점에 필요한 연결을 미리 해두고,**

**애플리케이션 종료 시점에 연결을 모두 종료하는 작업을 진행하려면, 객체의 초기화와 종료 작업이 필요하다.**

**이번 시간에는 스프링을 통해 이러한 초기화 작업과 종료 작업을 어떻게 진행하는지 예제로 알아보자.**

**간단하게 외부 네트워크에 미리 연결하는 객체를 하나 생성한다고 가정해보자.**

**실제로 네트워크에 연결하는 것은 아니고, 단순히 문자만 출력하도록 했다.**

**이 NetworkClient는 애플리케이션 시작 시점에 connect()를 호출해서 연결을 맺어두어야 하고,**

**애플리케이션이 종료되면 disConnect()를 호출해서 연결을 끊어야 한다.**

#### **1\. NetworkClient 클래스 생성**

```java
public class NetworkClient {

	private String url;

	//기본 Default 생성자
	public NetworkClient() { 
		System.out.println("생성자 호출, url = " + url);
		connect();
		call("초기화 연결 메시지");
	}

	// 외부에서 url을 넣을 수 있도록 Setter로 생성
	public void setUrl(String url) {
		this.url = url;
	}
	
	//서비스 시작시 호출
	public void connect() {
		System.out.println("connect : " + url);
	}

	//호출하고 call
	public void call(String message) {
		System.out.println("call" + url + "message" + message);
	}
	
	//서비스 종료시 호출
	public void disconnect() {
		System.out.println("close" + url);
	}
}
```

#### **2\. BeanLifeCycleTest 테스트 생성**

**close()를 사용하려면 ConfigurableApplicationContext가 필요하다.**

**왜냐하면,** **ApplicationContext를 사용할 때는, close를 할 일이 거의 없기 때문에 제공하지 않는다.**  
**따라서 close를 사용하려면 ConfigurableApplicationContext를 사용해야 한다.**  
**참고로 ConfigurableApplicationContext는 ApplicationContext를 상속받고 있다.**

```java
public class BeanLifeCycleTest {
	
	@Test
	public void lifeCycleTest() {
		ConfigurableApplicationContext ac = new AnnotationConfigApplicationContext(LifeCycleConfig.class);
		NetworkClient client = ac.getBean(NetworkClient.class);
		ac.close();

	}
	
	@Configuration
	static class LifeCycleConfig {
		
		
		//생성자의 결과물이 스프링 빈으로 등록이 된다.
		@Bean
		public NetworkClient networkClient() {
			NetworkClient networkClient = new NetworkClient();
			networkClient.setUrl("http://hello-spring.dev");
			return networkClient;
		}
	}
}
```

**3\. 결과**

**생성자 부분을 보면 url 정보 없이 connect가 호출되는 것을 확인할 수 있다.**

**너무 당연한 이야기이지만 객체를 생성하는 단계에는 url이 없고,**

**객체를 생성한 다음에 외부에서 수정자 주입을 통해서 setUrl()이 호출되어야 url이 존재하게 된다.**

```java
생성자 호출, url = null
connect : null
callnullmessage초기화 연결 메시지
```

### **☆중요★**

**스프링 빈은 간단하게 다음과 같은 라이프사이클을 가진다.**

**객체 생성 -> 의존관계 주입**

**스프링 빈은 객체를 생성하고, 의존관계 주입이 다 끝난 다음에야 필요한 데이터를 사용할 수 있는 준비가 완료된다.**

**(단, 생성자 주입은 예외이다. 생성자 주입은 처음 객체를 만들 때 스프링 빈이 같이 들어와야 하기 때문이다.)**

**따라서 초기화 작업은 의존관계 주입이 모두 완료되고 난 다음에 호출해야 한다.**

**그러면 개발자가 의존관계 주입이 모두 완료된 시점을 어떻게 알 수 있을까?**

**스프링은 의존관계 주입이 완료되면 스프링 빈에게서 콜백 메서드를 통해서 초기화 시점을 알려주는 다양한 기능을 제공한다. 또한 스프링은 스프링 컨테이너가 종료되기 직전에 소멸 콜백을 준다.**

**따라서 안전하게 종료 작업을 진행할 수 있다.**

* * *

#### **스프링 빈의 이벤트 라이프사이클**

**스프링 컨테이너 생성 -> 스프링 빈 생성 -> 의존관계 주입 -> 초기화 콜백 -> 사용 -> 소멸 전 콜백 -> 스프링 종료**

* * *

**초기화 콜백 : 빈이 생성되고, 빈의 의존관계 주입이 완료된 후 호출**

**소멸 전 콜백 : 빈이 소멸되기 직전에 호출**

* * *

> **참고 : 객체의 생성과 초기화를 분리하자.**  
> **생성자는 필수 정보(파라미터)를 받고, 메모리를 할당해서 객체를 생성하는 책임을 진다. 반면에 초기화는 이렇게 생성된 값들을 활용해서 외부 커넥션을 연결하는 등 무거운 동작을 수행한다.**  
> **따라서 생성자 안에서 무거운 초기화 작업을 함께 하는 것보다는 객체를 생성하는 부분과 초기화하는 부분을 명확하게  
> 나누는 것이 유지보수 관점에서 좋다.  
> 물론 초기화 작업이 내부 값들만 약간 변경하는 정도로 단순한 경우에는 생성자에서 한 번에 다 처리하는 게  
> 더 나을 수 있다.**

> **참고 : 싱글톤 빈들은 스프링 컨테이너가 종료될 때 싱글톤 빈들도 함께 종료되기 때문에 스프링 컨테이너가 종료되기 직전에 소멸 전 콜백이 일어난다. 뒤에서 설명하겠지만 싱글톤처럼 컨테이너의 시작과 종료까지 생존하는 빈도 있지만,  
> 생명주기가 짧은 빈들도 있는데 이 빈들은 컨테이너와 무관하게 해당 빈이 종료되기 직전에 소멸 전 콜백이 일어난다.  
> 자세한 내용은 스코프에서 알아보자.**

**스프링은 크게 3가지 방법으로 빈 생명주기 콜백을 지원한다.**

**1\. 인터페이스(InitializingBean, DisposableBean)**

**2\. 설정 정보에 초기화 메서드, 종료 메서드 지정**

**3\. @PostConstruct, @PreDestroy 어노테이션 지원**

**다음 시간부터 하나씩 공부해보자.**
