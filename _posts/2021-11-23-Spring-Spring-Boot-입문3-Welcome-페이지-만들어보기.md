---
title: "[Spring] Spring Boot 입문(3)- Welcome 페이지 만들어보기"
date: 2021-11-23 23:00:24 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["spring 기초", "welcome page", "뷰 리졸버", "스프링부트입문", "스프링입문"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Boot-%EC%9E%85%EB%AC%B83-Welcome-%ED%8E%98%EC%9D%B4%EC%A7%80-%EB%A7%8C%EB%93%A4%EC%96%B4%EB%B3%B4%EA%B8%B0"
---
resources 폴더안에 있는 static폴더안에 'index.html' 을 만들면 Welcome page 기능을 제공한다.

![](/assets/img/posts/19/1.png)

스프링은 자바의 생태계를 감싸고 있기에 머리속에 다 담을 수 가 없다.

그래서 필요한 기능을 찾아서 사용하는게 중요하다.

스프링 페이지에 들어가서 [https://spring.io/](https://spring.io/)  찾아서 사용하면 된다.

![](/assets/img/posts/19/2.png)

*Projects->Spring Boot -> Learn*

![](/assets/img/posts/19/3.png)

*Reference Doc*

![](/assets/img/posts/19/4.png)

메뉴얼에서 검색하면서 필요한 기능을 찾으면서 사용할 수 있다.

이렇게 정적인 페이지가 아니라 동적인 페이지를 만들기 위해서 템플릿엔진을 사용한다고한다.

강의에서는 Thymleaf를 사용한다.

![](/assets/img/posts/19/5.png)

*Template Engines에서 자주 사용되는 4가지*

**웹 애플리케이션에서 첫번째 진입점이 'Controller' 이다.** 그래서 Controller를 먼저 만들어보자.

![](/assets/img/posts/19/6.png)

*Controller은 Controller Anotation을 붙여줘야 한다.*

```java
package hello.hellospring.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HelloController {

	@GetMapping("hello") // http get 메소드
	public String hello(Model model) {
		model.addAttribute("data", "hello"); // Key : data \ Value : hello
		return "hello"; // model을 hello.html에 넘기고 hello페이지를 return한다.
       // Controller에서 return값으로 문자를 반환하면 뷰 리졸버(viewResolver)가 화면을 찾아서 처리
 
	}
}
```

웹 어플리케이션에서 /hello가 들어오면 'hello' 메소드를 호출한다.

templates폴더안에 HTML을 아래와 같이 만들어주면 타임리프를 이용하여 페이지를 제작할 수 있다.

![](/assets/img/posts/19/7.png)

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org"> <!-- 타임리프의 문법을 쓰기위한 추가 구문 -->
<head>
	<title>Hello</title>
	<meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
</head>
<body>

<p th:text="'안녕하세요.'+ ${data}">안녕하세요. 손님</p> <!-- Controller에 입력한 Value값으로 치환될것이다. -->

</body>
</html>
```

![](/assets/img/posts/19/8.png)

*결과*

![](/assets/img/posts/19/9.png)

1.  웹 브라우저에서 '/hello'를 던지면 스프링부트는 서버에서 받아서 helloController에서 해당하는 메소드를 실행한다.
2.  스프링이 model (key, value 넣어서) 이라는걸 만들어서 메소드에 넣어준다.
3.  return "hello"; 은 'templates'폴더 아래에 있는 'hello'파일을 찾아서 return한다.  
    **뷰 리졸버(viewResolver)가 화면을 찾아서 처리**한다. **'resources:templates/' + {ViewName}+'.html**  
    

출처 - 인프런 스프링 입문 강의
