---
title: "[Spring] Spring Framework - 핵심 원리 (48)  - 스코프와 Provider + ObjectProvider"
date: 2022-04-21 21:44:42 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["objectprovider", "request scope"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-48-%EC%8A%A4%EC%BD%94%ED%94%84%EC%99%80-Provider-ObjectProvider"
---
## **스코프와 Provider + **ObjectProvider**** 

* * *

**저번 정리 때, 스프링 애플리케이션을 실행시키면 오류가 발생했다. 스프링 애플리케이션을 실행하는 시점에 싱글톤 빈은 생성해서 주입이 가능하지만, request 스코프 빈은 아직 생성되지 않는다. 이 빈은 실제 고객의 요청이 와야 생성할 수 있다. 그렇다면 이런 문제점을 어떻게 해결할 수 있을까?**

### **스코프와 Provider**

**첫 번째 해결방안은 앞서 배운 Provider를 사용하는 것이다.**

**간단히 ObjectProvider를 사용해보자.**

#### **1\. Controller 수정**

**ObjectProvider <MyLogger>를 사용함으로써 원하는 MyLogger를 찾을 수 있는 객체를 주입받는다.**

```java
@Controller
@RequiredArgsConstructor
public class LogDemoController {

	private final LogDemoService logDemoService;
	//private final MyLogger myLogger;
	private final ObjectProvider<MyLogger> myLoggerProvider; // MyLogger를 찾을 수 있는 Logger를 주입받는다.
	
	@RequestMapping("log-demo") // "log-demo"라는 요청이오면 응답
	@ResponseBody // view파일을 사용하지 않고, String으로 바로 내보낼 수 있게 도와준다.
	public String logDemo(HttpServletRequest request) {
		String requestURL = request.getRequestURL().toString(); // 고객기 어떤 URL로 요청했는지 알 수 있다.
		
		MyLogger myLogger = myLoggerProvider.getObject();
		myLogger.setRequestURL(requestURL); 
		myLogger.log("controller test");
		
		logDemoService.logic("testId");
		
		return "OK";
	}
}
```

#### **2\. Service 수정**

```java
@Service
@RequiredArgsConstructor
public class LogDemoService {

	//private final MyLogger myLogger;
	private final ObjectProvider<MyLogger> myLoggerProvider;
	
	public void logic(String id) {
		
		MyLogger myLogger = myLoggerProvider.getObject();
		myLogger.log("service id = " + id); // 서비스로 넘어온 id 확인
	}
}
```

#### **3\. 결과 확인**

**새로고침을 해보면 각각의 다른 UUID가 생성되는 것을 확인할 수 있다.**

![](/assets/img/posts/125/1.png)

```java
[0c60703b-295d-46fa-9428-1feba423b7b2]request scope bean create : hello.core.common.MyLogger@50a3b911
[0c60703b-295d-46fa-9428-1feba423b7b2][http://localhost:8080/log-demo]controller test
[0c60703b-295d-46fa-9428-1feba423b7b2][http://localhost:8080/log-demo]service id = testId
[0c60703b-295d-46fa-9428-1feba423b7b2]request scope bean closed : hello.core.common.MyLogger@50a3b911
[914345bc-312f-494a-831b-eaaae58ba09f]request scope bean create : hello.core.common.MyLogger@70fa074a
[914345bc-312f-494a-831b-eaaae58ba09f][http://localhost:8080/log-demo]controller test
[914345bc-312f-494a-831b-eaaae58ba09f][http://localhost:8080/log-demo]service id = testId
[914345bc-312f-494a-831b-eaaae58ba09f]request scope bean closed : hello.core.common.MyLogger@70fa07
```

#### **정리**

-   **ObjectProvider 덕분에 ObjectProvider.getObject()를 호출하는 시점까지 requeset scope 빈의 생성을 지연할 수 있다.** **(정확하게는 스프링에 빈 생성을 지연을 하는 것이다.)**
-   **ObjectProvider.getObject()를 호출하는 시점에 HTTP 요청이 진행 중이므로 request scope 빈의 생성이 정상 처리된다.**
-   **ObjectProvider.getObject()를 LogDemoController, LogDemoService에서 각각 한 번씩 따로 호출해도 같은 HTTP 요청이면 같은 스프링이 반환된다!**
