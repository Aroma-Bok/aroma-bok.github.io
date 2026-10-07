---
title: "[Spring] Spring Framework - 핵심 원리 (49) - 스코프와 프록시 +웹 스코프와 프록시 동작 원리 + CGLIB"
date: 2022-04-26 22:05:17 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["cglib", "스코프와 프록시", "웹 스코프와 프록시 동작 원리"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-49-%EC%8A%A4%EC%BD%94%ED%94%84%EC%99%80-%ED%94%84%EB%A1%9D%EC%8B%9C-%EC%9B%B9-%EC%8A%A4%EC%BD%94%ED%94%84%EC%99%80-%ED%94%84%EB%A1%9D%EC%8B%9C-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC-CGLIB"
---
### **스코프와 프록시 +웹 스코프와 프록시 동작 원리 + CGLIB**

* * *

**이번에는 프록시 방식을 사용해보자.**

#### **1\. MyLogger 수정**

**기존 코드**

```java
@Component
@Scope(value="request") // 생존범위는 고객의 요청이 들어와서 나갈때까지다. 
public class MyLogger {
```

**프록시 방식을 사용한 코드**

```java
@Component
@Scope(value="request", proxyMode = ScopedProxyMode.TARGET_CLASS) // 생존범위는 고객의 요청이 들어와서 나갈때까지다. 
public class MyLogger {
```

**여기가 핵심이다.** 

**@Scope(value="request", proxyMode = ScopedProxyMode.TARGET\_CLASS) 코드처럼 수정하자.**

**적용 대상이 인터페이스가 아닌 클래스라면 'TARGET\_CLASS'를 선택**

**적용 대상이 인터페이스면 'INTERFACES'를 선택**

**이렇게 하면 MyLogger의 가짜 프록시 클래스를 만들어주고 HTTP request와 상관없이 가짜 프록시 클래스를 다른 빈에 미리 주입해둘 수 있다.**

#### **2\. Service 코드 수정**

**Service 코드 수정 전**

```java
@Service
@RequiredArgsConstructor
public class LogDemoService {

	
    private final ObjectProvider<MyLogger> myLoggerProvider;
	
	public void logic(String id) {
		
		MyLogger myLogger = myLoggerProvider.getObject();
		myLogger.log("service id = " + id); // 서비스로 넘어온 id 확인
	}
}
```

**Service 코드 수정 후**

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

#### **3\. Controller 수정**

**Controller 수정 전**

```java
@Controller
@RequiredArgsConstructor
public class LogDemoController {

	private final LogDemoService logDemoService;
	private final MyLogger myLogger;
//	private final ObjectProvider<MyLogger> myLoggerProvider; // MyLogger를 찾을 수 있는 Logger를 주입받는다.
	
	@RequestMapping("log-demo") // "log-demo"라는 요청이오면 응답
	@ResponseBody // view파일을 사용하지 않고, String으로 바로 내보낼 수 있게 도와준다.
	public String logDemo(HttpServletRequest request) {
		String requestURL = request.getRequestURL().toString(); // 고객기 어떤 URL로 요청했는지 알 수 있다.
		
		myLogger.setRequestURL(requestURL);
		
//		MyLogger myLogger = myLoggerProvider.getObject();
		myLogger.setRequestURL(requestURL); 
		myLogger.log("controller test");
		
		logDemoService.logic("testId");
		
		return "OK";
	}
}
```

**Controller 수정 후**

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

#### **4\. 실행결과**

**프록시 사용 전 에러**

```java
Error creating bean with name 'myLogger': Scope 'request' is not active for the current thread; consider defining a scoped proxy for this bean if you intend to refer to it from a singleton; nested exception is java.lang.IllegalStateException: No thread-bound request found: 
Are you referring to request attributes outside of an actual web request, or processing a request outside of the originally receiving thread? If you are actually operating within a web request and still receive this message, your code is probably running outside of DispatcherServlet:
In this case, use RequestContextListener or RequestContextFilter to expose the current request.
```

**프록시 사용 후**

```java
call AppConfig.memberRepository
call AppConfig.memberService
call AppConfig.orderService

[07f6ec0c-2733-475d-b734-a1332510632f]request scope bean create : hello.core.common.MyLogger@7185a899
[07f6ec0c-2733-475d-b734-a1332510632f][http://localhost:8080/log-demo]controller test
[07f6ec0c-2733-475d-b734-a1332510632f][http://localhost:8080/log-demo]service id = testId
[07f6ec0c-2733-475d-b734-a1332510632f]request scope bean closed : hello.core.common.MyLogger@7185a899
```

**마치 Provider를 사용하는 것처럼 똑같이 움직인다.**

* * *

#### **웹 스코프와 프록시 동작 원리**

**먼저 myLogger를 log로 찍어서 확인해보면 아래처럼 출력된다.**

```java
myLogger = class hello.core.common.MyLogger$$EnhancerBySpringCGLIB$$d4f52f2c
```

**CGLIB라는 라이브러리로 내 클래스를 상속받은 가짜 프록시 객체를 만들어서 주입한다.**

-   **@Scope의 proxyMode = ScopeProxyMode.TRAGET\_CLASS를 설정하면 스프링 컨테이너는  
    CGLIB라는 바이트코드를 조작하는 라이브러리를 사용해서, MyLogger를 상속받은 가짜 프록시 객체를 생성한다.**
-   **결과를 확인해보면 우리가 등록한 순수한 MyLogger 클래스가 아니라 MyLogger$$EnhancerBySpringCGLIB이라는 클래스로 만들어진 객체가 대신 등록된 것을 확인할 수 있다.**
-   **그리고 스프링 컨테이너에 'myLogger'라는 이름으로 진짜 대신에 이 까자 프록시 객체를 등록한다.**
-   **ac.getBean("myLogger", MyLogger.class)로 조회해도 프록시 객체가 조회되는 것을 확인할 수 있다.**
-   **그래서 의존관계 주입도 이 가짜 프록시 객체가 주입된다.**

![](/assets/img/posts/126/1.png)

#### **가짜 프록시 객체는 요청이 오면 그때 내부에서 진짜 빈을 요청하는 위임 로직이 들어있다.**

-   **가짜 프록시 객체는 내부에 진자 myLogger를 찾는 방법을 알고 있다.**
-   **클라이언트라 myLogger.logic()을 호출하면 사실은 가짜 프록시 객체의 메서드를 호출한 것이다.**
-   **가짜 프록시 객체는 request 스코프의 진짜 myLogger.logic()을 호출한다.**
-   **가짜 프록시 객체는 원본 클래스를 상속받아서 만들어졌기 때문에 이 객체를 사용하는 클라이언트 입장에서는 사실 원본인지 아닌지도 모르게, 동일하게 사용할 수 있다.(다형성)**

#### **동작 정리**

-   **CGLIB라는 라이브러리로 내 클래스를 상속받은 가짜 프록시 객체를 만들어서 주입한다.**
-   **이 가짜 프록시 객체는 실제 요청이 오면 그때 내부에서 실제 빈을 요청하는 위임 로직이 들어있다.**
-   **가짜 프록시 객체는 실제 request scope와는 관계가 없다. 그냥 가짜이고, 내부에 단순한 위임 로직만 있고, 싱글톤처럼 동작한다.**

#### **특징 정리**

-   **프록시 객체 덕분에 클라이언트는 마치 싱글톤 빈을 사용하듯이 편리하게 request socpe를 사용할 수 있다.**
-   **사실 Provider를 사용하든, 프록시를 사용하든 핵심 아이디어는 진짜 객체 조회를 꼭 필요한 시점까지 지연처리  
    한다는 점이다.**
-   **단지 어노테이션 설정 변경만으로 원본 객체를 프록시 객체로 대체할 수 있다. 이것이 바로 다형성과 DI 컨테이너가 가진 큰 장점이다.**
-   **꼭 웹 스코프가 아니어도 프록시는 사용할 수 있다.**

#### **주의점**

-   **마치 싱글톤을 사용하는 것 같지만 다르게 동작하기 때문에 결국 주의해서 사용해야 한다.**
-   **이런 특별한 scope는 꼭 필요한 곳에서만 최소화해서 사용하자, 무분별하게 사용하면 유지 보수하기 어려워진다.**
