---
title: "[Java] Java단에서 alert 띄우기 - HttpServletRequest 이용"
date: 2022-10-14 16:56:59 +0900
categories: ["Language", "Java"]
tags: ["httpservletrequest 이용해서 alert창 띄우기", "java단에서 alert창 띄우기"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-Java%EB%8B%A8%EC%97%90%EC%84%9C-alert-%EB%9D%84%EC%9A%B0%EA%B8%B0-HttpServletRequest-%EC%9D%B4%EC%9A%A9"
---
**개발을 하면서 Java단에서의 결과를 리턴해주기 위해서 alert창을 띄워야 할 때가 종종 있다.**

**물론 Ajax로 해도 되지만, 동기식으로 해야 할 때가 종종 있어서 어떤 방법이 있을까 하다가 같은 회사 과장님께 여쭤봐서 문제를 해결했다.**

**필자는 네이버, 카카오 등 간편 로그인 결과를 리턴해주기 위해서 alert창을 사용해야 했다.**

* * *

**HttpServletRequest을 이용해서 리턴 값을 화면단에 alert창을 띄울 수 있었다.**

**1\. Java 단에서 request.getSession().setAttribute()을 사용**

```java
public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

-생략 - 

request.getSession().setAttribute("msg","간편로그인이 해제되었습니다.");
}
```

**위처럼, key : value로 값을 넘겨준다.**

**2\. EL태그를 이용해서 값을 수신**

```java
<script type="text/javascript">

<c:if test="${not empty msg}">
	alert('${msg}');
</c:if>

</script>
```

**Java단에서 key 값으로 전달한 msg를 받아서 alert창을 띄울 수 있다.**
