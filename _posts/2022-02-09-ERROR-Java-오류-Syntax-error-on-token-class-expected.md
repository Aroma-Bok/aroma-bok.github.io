---
title: "[ERROR] Java 오류 - Syntax error on token \"class\", @ expected"
date: 2022-02-09 23:24:31 +0900
categories: ["ETC", "Error"]
tags: ["@ expected", "syntax error on token \"class\""]
tistory_url: "https://aroma-bok.tistory.com/entry/ERROR-Java-%EC%98%A4%EB%A5%98-Syntax-error-on-token-class-expected"
---
#### **가끔씩 'Syntax error on token "class", @ expected ' 에러를 볼 수 있을 것이다.**

#### **이유는 간단하다. 클래스를 메서드처럼 사용하려고 했기 때문이다.  아래 코드를 보자.**

```java
public class StatufulServiceTest {
	@Test
	void statefulServiceSingleton() {
	}
	
	static class TestConfig() { // --> 클래스를 메서드처럼 사용!!! '()'을 생략 해야한다.
		
		public StatefulService statefulService() {
			return new StatefulService();
		}
	}
}
```

#### **static class TestConfig()를 static class TestConfig로 변경하면 에러 해결이다.**

```java
public class StatufulServiceTest {
	@Test
	void statefulServiceSingleton() {
	}
	
	static class TestConfig {
		
		public StatefulService statefulService() {
			return new StatefulService();
		}
	}
		
}
```
