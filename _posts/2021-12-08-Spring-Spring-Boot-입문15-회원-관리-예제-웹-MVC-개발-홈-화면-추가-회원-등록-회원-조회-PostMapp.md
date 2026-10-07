---
title: "[Spring] Spring Boot - 입문(15) - 회원 관리 예제 - 웹 MVC 개발 + 홈 화면 추가 + 회원 등록 + 회원 조회 + PostMapping + Repository + Each"
date: 2021-12-08 23:10:41 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["each", "get", "post", "repository", "spring", "spring boot", "thymeleaf", "스프링 입문"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Boot-%EC%9E%85%EB%AC%B815-%ED%9A%8C%EC%9B%90-%EA%B4%80%EB%A6%AC-%EC%98%88%EC%A0%9C-%EC%9B%B9-MVC-%EA%B0%9C%EB%B0%9C-%ED%99%88-%ED%99%94%EB%A9%B4-%EC%B6%94%EA%B0%80-%ED%9A%8C%EC%9B%90-%EB%93%B1%EB%A1%9D-%ED%9A%8C%EC%9B%90-%EC%A1%B0%ED%9A%8C-PostMapping-Repository-Each"
---
#### **회원 관리 예제 - 웹 MVC 개발**

**회원 웹 기능 - 홈 화면 추가**

**회원 웹 기능 - 등록 / 조회**

* * *

Member 컨트롤러를 통해서 회원을 등록하고 조회하는 방법을 배워보자!

#### **1\. HomeController 생성**

```java
package hello.hellospring.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HomeController {

	
	@GetMapping("/") // '/'의 의미는 localhost8080 주소값
	public String home() {
		return "home"; // home.jsp를 찾아서 뷰 리졸버가 띄어준다.
	}
}
```

#### **2\. home.jsp 생성**

```java
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">

<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
<title>Insert title here</title>
</head>

<body>

<div class="container">

	<div>
		<h1>Hello Spring</h1> 
		
		<p>회원 기능</p>
		
		<p>
			<a href="/members/new">회원 가입</a> 
			<a href="/members">회원 목록</a>
		</p> 

	</div>

</div> 
<!-- /container --> 

</body>
</html>
```

근데 여기서 의문점이 하나 생겨야 한다. localhost:8080을 치고 들어가면, Welcome 페이지로 들어가야 하는 거 아닌가??

우선순위가 있다고 한다. 

요청이오면 먼저, Controller와 관련된 파일을 먼저 찾고 없으면 static 파일을 찾게 된다.

그래서 위에 컨트롤러에서는 'home.jsp'를 먼저 불러오게 되므로, Welcome페이지를 무시하게 된다.

#### **3\. MemberController에 GetMapping 하는 메서드 한 개 추가**

```java
	@GetMapping("/members/new") // /members/new 가 들어오면 매핑한다!
	public String createForm() {
		return "members/createMemberForm"; // createMemberForm.jsp를 찾아서 뷰 리졸버가 띄어준다.
	}
```

#### **4\. createMemberForm 페이지로 생성**

```java
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">

<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
<title>Insert title here</title>
</head>

<body>
<div class="container">

<form action="/members/new" method="post">

<div class="form-group">

<label for="name">이름</label>
<input type="text" id="name" name="name" placeholder="이름을입력하세요">

</div>
<button type="submit">등록</button> 
</form>

</div>
<!-- /container -->
</body>
</html>
```

![](/assets/img/posts/32/1.png)

*회원 등록 페이지로 이동한 화면*

**form, input에 의해서 이름에 내용을 집어넣으면 서버에 Key&Value를 보내준다고 한다.**

**\- input에서는 name태그가 중요하다. (매칭 되는 기준)**

#### **5\. MemberController에 @PostMapping추가** 

이름을 받아서 memberService.join에 넘겨준다.

```java
	@PostMapping("/members/new")
	public String create(MemberForm form) {
		Member member = new Member();
		member.setName(form.getName()); // form태그를 통해서 name값 받아온다.
		
		memberService.join(member); //받은 name을 join메서드로 보낸다.
		
		return "redirect:/"; // redirect:/ 의미 :  회원가입이 끝나면 홈 화면으로 보내는 의미
	}
```

\* **redirect:/** 는 다 완료되면 원하는 장소로 보내주는 의미인데, 여기서는 홈 화면으로 이동시킨다.

돌려보면 잘 작동한다!

**원리**

**1\. 회원 가입 클릭 (members/new)** 

**2\. 컨트롤러 작동 @GetMapping("/members/new")**

**3\. members/createMemberForm 호출 -> form, input을 통해서 'name' 전달 + PostMapping호출** 

**4\. MemberForm안에 name값에 값 전달 (setName을 통해서)**

**Get 방식 : URL에 직접 표시되는 방법 + 조회**

**Post 방식 : Data를 Form에 넣어서 전달할 때 사용**

#### **6\. 회원 리스트 보여주는 Controller 등록**

```java
	@GetMapping
	public String list(Model model) {
		List<Member> members = memberService.findMembers(); // 등록된 회원 리스트 조회
		model.addAttribute("members", members); //List 자체를 model에 담아준다!
		return "members/memberList";
		
	}
```

#### **7\. memberList.jsp 생성**

여기서 thymeleaf의 기능이 쓰인다고 한다!

```java
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org"> 

<body>

<div class="container">
<div>

<table>
<thead>
<tr>
<th>#</th> 
<th>이름</th>
</tr>
</thead>

 <tbody>
<tr th:each="member : ${members}">
<td th:text="${member.id}"></td> 
<td th:text="${member.name}"></td>
</tr> 
</tbody>

</table> 
</div>
</div> 
<!-- /container --> 
</body>
</html>
```

확인해보면 아직 등록되어 있는 회원이 없어서 아무것도 안 뜬다!

등록해보면 등록된 순서에 따라서 저장이 되는 것을 볼 수 있다.

![](/assets/img/posts/32/2.png)

*결과*

```java
<tr th:each="member : ${members}">
<td th:text="${member.id}"></td> 
<td th:text="${member.name}"></td>
```

이 코드를 한 개 넣어놨는데, 어째서 계속 생길 수 있는 걸까? 

바로 **thymeleaf**가 관여해서 도와주기 때문이다.

**원리**

**1\. ${members}는 model안에 있는 Data를 갖고 온다.**

**2\. thymeleaf의 each문법은 루프를 돌면서 값을 조회해주는 기능**

**3\. 그래서 model안에서 하나씩 뽑아서 member에 넣어주고 하나씩 id와 name을 넣어서 만들어준다.**

여기서 필자는 Data를 DB안에다만 저장해서 뽑아오는 방법만 해봤기에 DB가 없이 이렇게 돌아가는 상황이 신기했다.

이렇게 Repository를 사용하는 건지 처음 알았다.

하지만, Server에 저장되어있기 때문에 서버를 재실행하면 Data가 삭제된다(메모리에 저장되는 거라 Reset 된다).

(실무에서 이렇게 했다간 큰일ㅎㅎㅎ)
