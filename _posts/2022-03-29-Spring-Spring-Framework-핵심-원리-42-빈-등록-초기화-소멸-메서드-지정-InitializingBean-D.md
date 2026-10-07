---
title: "[Spring] Spring Framework - 핵심 원리 (42) - 빈 등록 초기화, 소멸 메서드 지정 + InitializingBean, DisposableBean + 빈(Bean) 기능 + @PostConstruct, @PreDestroy 어노테이션 사용법"
date: 2022-03-29 22:40:09 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["@postconstruct", "@postconstruct @predestroy 특징 사용법", "@predestroy", "disposablebean", "initializingbean", "빈 생명주기 콜백", "빈(bean) 생명주기 콜백지원하는 방법"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-42-%EB%B9%88-%EB%93%B1%EB%A1%9D-%EC%B4%88%EA%B8%B0%ED%99%94-%EC%86%8C%EB%A9%B8-%EB%A9%94%EC%84%9C%EB%93%9C-%EC%A7%80%EC%A0%95-InitializingBean-DisposableBean-%EB%B9%88Bean-%EA%B8%B0%EB%8A%A5-PostConstruct-PreDestroy-%EC%96%B4%EB%85%B8%ED%85%8C%EC%9D%B4%EC%85%98-%EC%82%AC%EC%9A%A9%EB%B2%95"
---
### **빈 등록 초기화, 소멸 메서드 지정 + InitializingBean, DisposableBean + 빈(Bean) 기능 + @PostConstruct, @PreDestroy 어노테이션 사용법**

* * *

**앞서 공부한 내용에서 스프링의 빈(Bean) 생명주기 콜백 지원하는 방법에는 3가지가 있다고 공부했다.**

**1\. 인터페이스(InitializingBean, DisposableBean)**

**2\. 설정 정보에 초기화 메서드, 종료 메서드 지정**

**3\. @PostConstruct, @PreDestroy 어노테이션 지원**

### **1\. 인터페이스(InitializingBean, DisposableBean)**

#### **1. **InitializingBean와 DisposableBean를 상속****

**가장 먼저 저번 내용에서 만들어둔 NetworkClient에 InitializingBean와 DisposableBean를 상속받자.**

**먼저 InitializingBean 인터페이스 내부를 확인해보자. 간단하게 작성되어 있는 것을 확인할 수 있다.**

**afterPropertiesSet는 의존관계가 끝나면 호출해주겠다는 의미이다.**

```java
public interface InitializingBean {

	void afterPropertiesSet() throws Exception;

}
```

**그리고 다음으로는 DisposableBean 인터페이스를 보자. destroy 하나 있다. 간단하다.**

```java
public interface DisposableBean {

	void destroy() throws Exception;

}
```

```java
public class NetworkClient implements InitializingBean, DisposableBean {

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
	
	@Override
	public void afterPropertiesSet() throws Exception {
		connect();
		call("초기화 연결 메시지");
	}
	
	@Override
	public void destroy() throws Exception {
		disconnect();
	}
}
```

#### **2\. 테스트 결과**

**출력 결과를 보면 초기화 메서드가 주입 완료 후에 적절하게 호출된 것을 확인할 수 있다.**

**그리고 스피링 컨테이너의 종료가 호출되자 소멸 메서드가 호출된 것도 확인할 수 있다.**

```java
생성자 호출, url = null
connect : null
callnullmessage초기화 연결 메시지
connect : http://hello-spring.dev
callhttp://hello-spring.devmessage초기화 연결 메시지
21:43:35.595 [main] DEBUG org.springframework.context.annotation.AnnotationConfigApplicationContext - Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@d737b89, started on Tue Mar 29 21:43:34 KST 2022
closehttp://hello-spring.dev
```

#### **3\. 정리**

**Initializing은 afterPropertiesSet() 메서드로 초기화를 지원한다.**

**DisposableBean은 destroy() 메서드로 소멸을 지원한다.**

#### **초기화, 소멸 인터페이스 단점**

**이 인터페이스는 스프링 전용 인터페이스다. 해당 코드가 스프링 전용 인터페이스에 의존한다.**

**초기화, 소멸 메서드의 이름을 변경할 수 없다. (afterPropertiesSet, destroy 이름 변경 불가능)**

**내가 코드를 고칠 수 없는 외부 라이브러리에 적용할 수 없다.(E.g : Maven, Gradle에서 다운로드한 라이브러리)**

> **참고 : 인터페이스를 사용하는 초기화, 종료 방법은 스프링 초창기에 나온 방법들이고, 지금은 다음의 더 나은 방법들이 있어서 거의 사용하지 않는다.**

* * *

### **2\. 설정 정보에 초기화 메서드, 종료 메서드 지정 - 빈(Bean)의 기능**

**설정 정보에 @Bean(initMethod = "init", destroyMethod = "close")처럼 초기화, 소멸 메서드를 지정할 수 있다.**

#### **1\. 'initMethod'와 'destroyMethod'를 생성**

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
	
	public void init() {
		System.out.println("NetworkClient.init");
		connect();
		call("초기화 연결 메시지");
	}
	
	public void close() {
		System.out.println("NetworkClient.close");
		disconnect();
	}
}
```

#### **2\. @Bean에 'initMethod'와 'destroyMethod' 부여**

```java
	@Configuration
	static class LifeCycleConfig {
		
		//생성자의 결과물이 스프링 빈으로 등록이 된다.
		@Bean(initMethod = "init", destroyMethod = "close")
		public NetworkClient networkClient() {
			NetworkClient networkClient = new NetworkClient();
			
			//객체를 생성하고 Setter로 주입
			networkClient.setUrl("http://hello-spring.dev");
			return networkClient;
		}
	}
```

#### **3\. 테스트 결과**

```java
생성자 호출, url = null
connect : null
callnullmessage초기화 연결 메시지
NetworkClient.init
connect : http://hello-spring.dev
callhttp://hello-spring.devmessage초기화 연결 메시지
21:56:29.850 [main] DEBUG org.springframework.context.annotation.AnnotationConfigApplicationContext - Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@d737b89, started on Tue Mar 29 21:56:29 KST 2022
NetworkClient.close
closehttp://hello-spring.dev
```

#### **4\. 정리**

#### **설정 정보 사용 특징**

-   **메서드 이름을 자유롭게 줄 수 있다.**
-   **스프링 빈이 스프링 코드에 의존하지 않는다.**
-   **코드가 아니라 설정 정보를 사용하기 때문에 코드를 고칠 수 없는 외부 라이브러리에도 초기화, 종료 메서드를 적용할 수 있다.**

#### **참고 :종료 메서드 추론**

-   **@Bean의 destroyMethod 속성에는 아주 특별한 기능이 있다.**
-   **라이브러리는 대부분 close, shutdown이라는 이름의 종료 메서드를 사용한다.**
-   **@Bean의 destroy는 기본값이 (inferred) (추론)으로 등록되어 있다.**
-   String destroyMethod() default AbstractBeanDefinition.INFER\_METHOD;
-   **이 추론 기능은, close, shutdown라는 이름의 메서드를 자동으로 호출해준다.  
    이름 그대로 종료 메서드를 추론해서 호출해준다.**
-   **따라서 직접 스프링 빈으로 등록하면 종료 메서드는 따로 적어주지 않아도 잘 동작한다.**
-   **추론 기능을 사용하기 싫으면 destroyMethod=""처럼 빈 공백을 지정하면 된다.**

* * *

### **3.@PostConstruct, @PreDestroy 어노테이션 지원**

**결론부터 말하면, 이 방법을 사용하자! 스프링에서도 이 방법을 권고하고 있다.**

#### **1\. @PostConstruct & @PreDestroy 부여**

```java
	@PostConstruct
	public void init() {
		System.out.println("NetworkClient.init");
		connect();
		call("초기화 연결 메시지");
	}
	
	@PreDestroy
	public void close() {
		System.out.println("NetworkClient.close");
		disconnect();
	}
```

#### **2\. @Bean 원상복귀**

```java
	@Configuration
	static class LifeCycleConfig {
		
		//생성자의 결과물이 스프링 빈으로 등록이 된다.
		@Bean
		public NetworkClient networkClient() {
			NetworkClient networkClient = new NetworkClient();
			
			//객체를 생성하고 Setter로 주입
			networkClient.setUrl("http://hello-spring.dev");
			return networkClient;
		}
	}
```

#### **3\. 테스트 결과**

```java
생성자 호출, url = null
connect : null
callnullmessage초기화 연결 메시지
NetworkClient.init
connect : http://hello-spring.dev
callhttp://hello-spring.devmessage초기화 연결 메시지
22:11:23.475 [main] DEBUG org.springframework.context.annotation.AnnotationConfigApplicationContext - Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@d737b89, started on Tue Mar 29 22:11:22 KST 2022
NetworkClient.close
closehttp://hello-spring.dev
```

#### **4\. 정리**

**@PostConstruct, @PreDestroy 이 두 어노테이션을 사용하면 가장 편리하게 초기화와 종료를 실행할 수 있다.**

### **@PostConstruct, @PreDestroy 어노테이션 특징**

1.  **최신 스프링에서 가장 권장하는 방법이다.**
2.  **어노테이션 하나만 붙이면 되므로 매우 편리하다.**
3.  **패키지를 잘 보면, javax.annotation, PostConstruct이다. 스프링에 종속적인 기술이 아니라, JSR-250이라는 자파 표준이다. 따라서 스프링이 아닌 다른 컨테이너에서도 동작한다.**
4.  **컴포넌트 스캔과 잘 어울린다.**
5.  **유일한 단점은, 외부 라이브러리에는 적용하지 못한다는 것이다. 외부 라이브러리를 초기화, 종료해야 하는**  
    **경우에는 @Bean의 기능을 사용하자.** 

### **총 정리**

**1\. @PostConstruct, @PreDestroy 어노테이션을 사용하자.**

**2\. 코드를 고칠 수 없는 외부 라이브러리를 초기화, 종료해야 한다면,  
@Bean의 'initMethod' & 'destroyMethod' 기능을 사용하자.**
