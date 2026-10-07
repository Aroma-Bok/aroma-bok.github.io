---
title: "[Spring] Spring Boot - 입문(7) - API 방식?"
date: 2021-11-29 23:48:26 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["api방식", "responsebody", "스프링", "스프링부트", "템플릿엔진"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Boot-%EC%9E%85%EB%AC%B87-API-%EB%B0%A9%EC%8B%9D"
---
**웹을 제작하는데 있어서 정적 컨텐츠를 제외하고는 이 2가지가 있다고 합니다.**

**1\. 템플릿엔진을 통해서 화면을 랜더링해서 HTML을 웹브라우저에 넘겨주는 방식**

**2\. API 사용하는 방식 => 객체를 반환하는 방식 (객체를 JSON으로 반환)**

```java
	@GetMapping("hello-string")
	@ResponseBody // HTTP 헤더, 바디부에서 바디부에 직접 넣어주겠다는 의미
	public String helloString(@RequestParam("name") String name) {
		return "hello " + name; // 위에랑 다른 점은, View가 없이 그대로 내려간다.
	}
```

![](/assets/img/posts/23/1.png)![](/assets/img/posts/23/2.png)

*결과*

**@ResponseBody** 는 **HTML의 Body부에 직접 넣어주겠다라는 의미**라서, 소스 코드를 봐도 Body부만 표시됩니다.

**그리고 진짜 중요한 부분은 아래부터 입니다.**

```java
	@GetMapping("hello-api")
	@ResponseBody 
	public Hello helloApi(@RequestParam("name") String name){
		Hello hello = new Hello();
		hello.setName(name);
		return hello;
	}
		
	static class Hello { // 이러한 접근 방식을 'Property 방식'이라고도 한다.
		private String name;
		
		public String getName() { // 꺼낼 때
			return name;
		}
		
		public void setName(String name) { // 넣을때 
			this.name = name;
		}
		
	}
```

![](/assets/img/posts/23/3.png)![](/assets/img/posts/23/4.png)

**결과**가 **JSON (Key : Value로 이루어진 포맷)**으로 나온다.

※ 옛날엔 XML방식을 사용했지만, 무겁고 번거로워서 JSON 방식을 선호하고 기본이 되었다고 합니다.

※ Get(게터) , Set(t세터) - Java Bean 규약

**설명**

![](/assets/img/posts/23/5.png)

앞서 배웠던 내용에서는, @ResponseBody가 없다면, 일반적으로 ViewResolver에게 던지지만,

@RequestBody가 있다면, HTTP 응답에 그대로 넘겨야한다고 인지하고 작동 합니다.

하지만 return이 단순히 이름이 아니라, 객체가 된다면 default가 JSON으로 HTTP 응답에 반환하는것이다

![](/assets/img/posts/23/6.png)

정리하면, 

@ResponseBody가 있으면 HttpMessageConverter 가 동작한다.

기본 문자처리라면, StringConverter (StringHttpMessageConverter)가 동작

기본 객체처리라면, JsonConverter (MappingJackson2HttpMessageConverter)가 동작

※ **객체를 JSON으로 바꿔주는 라이브러리**가 대표로 2가지가 있다.

**1\. Jackson**

**2\. GSON**

**\-스프링부트에서는 Jackson이 내재되어 있다.**

참고 : 클라이언트의 HTTP Accept 헤더와 서버의 컨트롤러 반환 타입 정보 둘을 조합해서 

HttpMessageConverter가 선택된다. (자세한 내용은 나중에 MVC를 공부할 때 배울 수 있다...)
