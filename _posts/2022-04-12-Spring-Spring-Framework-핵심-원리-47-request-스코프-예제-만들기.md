---
title: "[Spring] Spring Framework - 핵심 원리 (47) - request 스코프 예제 만들기"
date: 2022-04-12 23:45:16 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["request scope", "스프링 부트 서버 변경하는 법", "스프링 부트 포트 변경"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-47-request-%EC%8A%A4%EC%BD%94%ED%94%84-%EC%98%88%EC%A0%9C-%EB%A7%8C%EB%93%A4%EA%B8%B0"
---
### **request 스코프 예제 만들기** 

* * *

**웹 스코프는 웹 환경에서만 동작하므로 web 환경이 동작하도록 라이브러리를 추가하자.**

**build.gradle에 아래 코드를 적용하자.**

```java
	//web 라이브러리 추가
	implementation 'org.springframework.boot:spring-boot-starter-web'
```

**늘 말하지만, build.gradle에 추가하면, Gradle->Refresh Gradle Project를 눌러주자.**

**그리고 라이브러리를 확인해보면, spring-web과 관련된 기술들이 들어온 것을 확인할 수 있다.**

![](/assets/img/posts/124/1.png)

**이제 hello.core.CoreApplication의 main 메서드를 실행하면 웹 애플리케이션이**

**실행되는 것을 확인할 수 있다.**

```java
2022-04-12 22:19:26.834  INFO 5036 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2022-04-12 22:19:26.853  INFO 5036 --- [           main] hello.core.CoreApplication               : Started CoreApplication in 5.57 seconds (JVM running for 6.922)
```

**예전과는 다르게, 'port(s) : 8080'이라는 게 보인다.**

> **참고 : spring-boot-stater-web 라이브러리를 추가하면 스프링 부트는 내장 톰캣 서버를 활용해서 웹 서버와 스프링을 함께 실행시킨다.** 

> **참고 : 스프링 부트는 웹 라이브러리가 없으면 우리가  지금까지 학습한 AnnotationConfigApplicationContext을 기반으로 애플리케이션을 구동한다. 웹 라이브러리가 추가되면, 웹과 관련된 추가 설정과 환경들이 필요하므로 AnnotationConfigServletWebServerApplicationContext를 기반으로 애플리케이션을 구동한다.**

**만약 기본 포트인 8080 포트를 다른 곳에서 사용 중이어서 오류가 발생하면 포트를 변경해야 한다.**

**9090 포트로 변경하려면 다음 설정을 추가하자.** 

**먼저 'main/resources/application.properties'에 들어가서 아래 코드를 작성하면 된다.**

**※스프링 부트 포트 변경하는 법**

```java
server.port=9090
```

**동시에 여러 HTTP 요청이 오면 정확히 어떤 요청이 남긴 로그인지 구분하기 어렵다.**

**이럴 때 사용하기 딱 좋은 것이 바로 request 스코프이다.** 

**다음과 같이 로그가 남도록 request 스코프를 활용해서 추가 기능을 개발해보자.**

```java
[d06b992f...] request scope bean create
[d06b992f...][http://localhost:8080/log-demo] controller test
[d06b992f...][http://localhost:8080/log-demo] service id = testId
[d06b992f...] request scope bean close
```

**기대하는 공통 포맷 : \[UUID\]\[requstURL\]{message}**

**UUID를 사용해서 HTTP 요청을 구분하자.**

**requestURL 정보도 추가로 넣어서 어떤 URL을 요청해서 남은 로그인지 확인하자.**

**먼저 코드로 확인해보자.**

#### **1\. MyLogger** 

```java
@Component
@Scope(value="request")
public class MyLogger {
	
	private String uuid;
	private String requestURL; // 나중에 중간에 들어올 수 있도록 Setter로 설정해줄것이다.
	
	public void setRequestURL(String requestURL) {
		this.requestURL = requestURL;
	}
	
	public void log(String message) {
		System.out.println("[" + uuid + "]" + "[" + requestURL + "]" +  message );
	}
	
	
	@PostConstruct
	public void init() {
		uuid = UUID.randomUUID().toString();
		System.out.println("[" + uuid + "]" + "request scope bean create : " + this);
	}
	
	@PreDestroy
	public void close() {
		System.out.println("[" + uuid + "]" + "request scope bean closed : " + this);
		
	}
}
```

-   **로그를 출력하기 위한 MyLogger 클래스이다.**
-   **@Scope(value = "request")를 사용해서 request 스코프로 지정했다.  
    이제 이 빈(Bean)은 HTTP 요청 당, 하나씩 생성되고, HTTP 요청이 끝나는 시점에 소멸된다.**
-   **이 빈(Bean)이 생성되는 시점에 자동으로 @PostConstruct 초기화 메서드를 사용해서 uuid를 생성해서 저장해둔다. 이 빈(Bean)은 HTTP 요청 당 하나씩 생성되므로, uuid를 저장해두면 다른 HTTP 요청과 구분할 수 있다.**
-   **이 빈(Bean)이 소멸되는 시점에 @PreDestroy를 사용해서 종료 메시지를 남긴다.**
-   **requestURL은 이 빈(Bean)이 생성되는 시점에는 알 수 없으므로, 외부에서 setter로 입력받는다.**

#### **2\. LogDemoController**

```java
@Controller
@RequiredArgsConstructor
public class LogDemoController {

	private final LogDemoService logDemoService;
	private final MyLogger myLogger;
	
	@RequestMapping("log-demo") // "log-demo"라는 요청이오면 응답
	@ResponseBody // view파일을 사용하지 않고, String으로 바로 내보낼 수 있게 도와준다.
	public String logDemo(HttpServletRequest request) {
		String requestURL = request.getRequestURL().toString(); // 고객기 어떤 URL로 요청했는지 알 수 있다.
		
		myLogger.setRequestURL(requestURL); 
		myLogger.log("controller test");
		
		logDemoService.logic("testId");
		
		return "OK";
	}
}
```

-   **Logger가 잘 작동하는지 확인하는 테스트용 컨트롤러이다.**
-   **여기서 HttpServletRequest를 통해서 요청 URL을 받았다.  
    \*requestURL 값 : http://localhost:8080//log-demo\***
-   **이렇게 받은 requestURL 값을 myLogger에 저장해둔다. myLogger는 HTTP 요청 당 각각 구분되므로  
    다른 HTTP 요청 때문에 값이 섞이는 걱정은 하지 않아도 된다.**
-   **컨트롤러에서 controller test라는 로그를 남긴다.**

> **참고 : requestURL을 MyLogger에 저장하는 부분은 컨트롤러보다는 공통 처리가 가능한 스프링 인터셉트나 서블릿 필터 같은 곳을 활용하는 것이 좋다. 여기서는 예제를 단순화하고, 아직 스프링 인터셉터를 학습하지 않았기 때문에 컨트롤러를 사용했다. 스프링 웹이 익숙하다면 인터셉터를 사용해서 구현해보자.**

#### **3\. LogDemoService**

```java
@Service
@RequiredArgsConstructor
public class LogDemoService {

	private final MyLogger myLogger;
	
	public void logic(String id) {
		myLogger.log("service id = " + id); // 서비스로 넘어온 id 확인
	}
}
```

-   **비즈니스 로직이 있는 서비스 계층에서도 로그를 출력해보자.**
-   **여기서 중요한 점이 있다. request scope를 사용하지 않고 파라미터로 이 모든 정보를 서비스 계층에 넘긴다면, 파라미터가 많아서 지저분해진다. 더 문제는 requestURL 같은 웹과 관련된 정보가 웹과 관련 없는 서비스 계층까지 넘어가게 된다. 웹과 관련된 부분은 Controller 까지만 사용해야 한다. 서비스 계층은 웹 기술에 종속되지 않고, 가급적 순수하게 유지하는 것이 유지보수 관점에서 좋다.**
-   **request scope의 MyLogger 덕분에 이런 부분을 파라미터로 넘기지 않고, MyLogger의 멤버 변수에 저장해서 코드와 계층을 깔끔하게 유지할 수 있다.** 

**이제 실행을 해보자.**

**기대하는 출력**

```java
[d06b992f...] request scope bean create
[d06b992f...][http://localhost:8080/log-demo] controller test
[d06b992f...][http://localhost:8080/log-demo] service id = testId
[d06b992f...] request scope bean close
```

**실제는 기대와 다르게 애플리케이션 실행 시점에 오류 발생**

```java
Error creating bean with name 'myLogger': Scope 'request' is not active for the 
current thread; consider defining a scoped proxy for this bean if you intend to refer to 
it from a singleton;
```

**원인이 무엇일까?**

**자 컨트롤러부터 확인해보자.**

**MyLogger는 'request scope'를 사용했다.  
이 말은 즉, '사용자의 요청이 들어오고 나갈 때까지 생존 가능한 빈'이다.**

**하지만, 스프링이 실행되고 컴포넌트 스캔(Component Scan)을 하려고 해도, 사용자의 요청이 들어오지 않은 상태이기 때문에 빈(Bean)을 만들 수 없는 게 정상이다. 그래서 오류가 발생하는 것이다.**

```java
@Controller
@RequiredArgsConstructor
public class LogDemoController {

	private final LogDemoService logDemoService;
	private final MyLogger myLogger;
```

* * *

#### **정리**

**스프링 애플리케이션을 실행시키면 오류가 발생한다. 메시지 마지막에 싱글톤이라는 단어가 나오고 있다.**

**스프링 애플리케이션을 실행하는 시점에 싱글톤 빈을 생성해서 주입이 가능하지만, request 스코프 빈은 아직 생성되지 않는다. 이 빈은 실제 고객의 요청이 와야 생성할 수 있다.**

**그러면 어떻게 해결할 수 있을까?**

**저번에 공부했던, Provider를 사용해서 할 수 있다!**

**다음 시간에 공부해보자.**
