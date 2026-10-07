---
title: "[Spring] Spring Boot - 입문(6) - 스프링 웹 개발 기초 [MVC와 템플릿 엔진]"
date: 2021-11-26 00:15:05 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["thymeleaf", "스프링", "스프링기초", "스프링부트", "스프링부트 기초", "타임리프"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Boot-%EC%9E%85%EB%AC%B86-%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9B%B9-%EA%B0%9C%EB%B0%9C-%EA%B8%B0%EC%B4%88-MVC%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%97%94%EC%A7%84"
---
MVC : Model, View, Controller

과거에는 View와 Controller가 분리되어 있지않고, View에서 다 처리(JSP사용해서) 하는 'Model1' 방식이었다.

MVC처럼 나눠주는 이유는, 각각의 역할이 있다.

Controller - 비즈니스 로직, 서버와 관련된 일을 처리하는데 집중

View - 화면을 그리는데에 모든 역량을 집중

![](/assets/img/posts/22/1.png)

Thymeleaf의 장점 : 서버없이 HTML을 오픈해서 볼 수 있다.

![](/assets/img/posts/22/2.png)

http://localhost:8080/hello-mvc을 URL에 치고 들어가면 아래와 같이 에러가 발생한다.

![](/assets/img/posts/22/3.png)

*Required request parameter 'name' for method parameter type String is not present*

```java
@GetMapping("hello-mvc")
	public String helloMvc(@RequestParam(name = "name", required=true) String name, Model model) { //외부에서 파라미터를 받을때 @RequestParam사용 | 위에는 Value를 직접 받았는데, 여기서는 Model에 담아진걸 받는다.
		model.addAttribute("name", name);
		return "hello-template"; //Model을 hello-template.html에 넘기고 템플릿에 있는 페이지를 리턴
		
		//required는 기본이 true이기 때문에 값을 넘겨야한다. 
        //그래서 Required request parameter 'name' for method parameter type String is not present 
        //에러가 발생한다.
	}
```

So, URL에 아래와 같이 ?를 붙이고 name에 대한 value를 넣어주면 Controller에서 인식한다!!!

![](/assets/img/posts/22/4.png)

(\* 에러가 나면 콘솔에서 확인하는 습관을 기르자!)

#### **설명**

name = spring!!!!!이라는 값으로 바뀌게 되고, 이 값이 hello-template.HTML 로 넘어가게 된다.

그리고 이 값은 Model의 키 값인 ${name}에 spring!!!!! 값이 들어간다.

![](/assets/img/posts/22/5.png)

**1\. Controller에서 매핑된 메소드를 호출**

**2\. model을 Spring에 넘겨주고, viewResolver가 움직이면서 해당 html을 연결시켜준다.**

**3\. viewResolver에서 변환 후 넘겨준다.**

![](/assets/img/posts/22/6.png)

*변환 후 넘겨받은 값*

![](/assets/img/posts/22/7.png)
