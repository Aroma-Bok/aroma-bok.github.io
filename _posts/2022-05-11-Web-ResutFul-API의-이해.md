---
title: "[Web] ResutFul API의 이해"
date: 2022-05-11 22:33:14 +0900
categories: ["Solution & Tools", "Web"]
tags: ["parsebodymethods", "resutful", "resutful api", "resutful 이란?"]
tistory_url: "https://aroma-bok.tistory.com/entry/Web-ResutFul-API%EC%9D%98-%EC%9D%B4%ED%95%B4"
---
## **ResutFul API의 이해**

* * *

#### **Rest란?**

**: HTTP 통신에서 어떤 자원에 대한 CRUD 요청을 URL과 Method로 표현하여 특정한 형태로 전달하는 방식.**

#### **장점**

-   **요청을 위한 URL과 이에 대한 Method, 자원의 형태(JSON)를 사용하면 표현이 명확해지므로 인식하기 좋다.**
-   **이러한 규칙을  지켜서 설계된 API를 Rest APU 또는 Restful 한 API라고 한다.**

#### **역할**

-   **Restful API는 스마트폰 App이나 Ajax 기반의 웹 페이지가 Database에 접근하기 위한 통로로서의 역할을 한다.**
-   **Restful은 클라이언트가 구성해야 할 UI에 필요한 데이터를 제공하거나 입력, 수정, 삭제 등의 요청을 수행하고 결과만을 알려주기 때문에 직접 UI를 갖는 페이지를 만들지는 않는다.**

![](/assets/img/posts/129/1.png)

#### **일반 API와의 비교**

**일반 API는 전송방식에 상관없이 개별적인 URL로 CRUD를 구분한다.**

**Restful은 하나의 URL에 HTTP 전송방식으로 CRUD를 구분한다.**

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft"><tbody><tr><td style="width: 25%;">&nbsp;</td><td style="width: 25%;"><b><span style="font-family: 'Noto Serif KR';">일반 API</span></b></td><td style="width: 50%;"><b><span style="font-family: 'Noto Serif KR';">Restful API</span></b></td></tr><tr><td style="width: 25%;"><b><span style="font-family: 'Noto Serif KR';">CREATE</span></b></td><td style="width: 25%;"><b><span style="font-family: 'Noto Serif KR';">[POST] /api/add_x</span></b></td><td style="width: 50%;"><b><span style="font-family: 'Noto Serif KR';">[POST] /api/x</span></b></td></tr><tr><td style="width: 25%;"><b><span style="font-family: 'Noto Serif KR';">READ&nbsp;</span></b></td><td style="width: 25%;"><b><span style="font-family: 'Noto Serif KR';">[GET] /api/get_x</span></b></td><td style="width: 50%;"><b><span style="font-family: 'Noto Serif KR';">[GET]&nbsp; /api/x</span></b></td></tr><tr><td style="width: 25%;"><b><span style="font-family: 'Noto Serif KR';">UPDATE&nbsp;</span></b></td><td style="width: 25%;"><b><span style="font-family: 'Noto Serif KR';">[POST] /api/edit_x</span></b></td><td style="width: 50%;"><b><span style="font-family: 'Noto Serif KR';">[PUT]&nbsp; /api/x</span></b></td></tr><tr><td style="width: 25%;"><b><span style="font-family: 'Noto Serif KR';">DELETE&nbsp;</span></b></td><td style="width: 25%;"><b><span style="font-family: 'Noto Serif KR';">[POST] /api/delete_x</span></b></td><td style="width: 50%;"><b><span style="font-family: 'Noto Serif KR';">[DELETE] /api/x</span></b></td></tr></tbody></table>

**HTML과 웹 브라우저는 POST와 GET방식만을 지원하기 때문에, Restful은 스마트폰 App과 같은 웹 이외의 프로그램에서 사용하는 것이 보통이다.**

**Ajax를 사용할 경우 직접 PUT, DELETE 방식을 추가로 정의하여 Restful Client를 개발할 수 있다.**

#### **Restful을 위한 톰캣 서버 설정**

**톰켓(Tomcat)은 기본적으로 PUT, DELETE 방식의 전송을 식별하지 못하기 때문에 서버 설정 파일인 server.xml을 수정해야 한다. (parseBodyMethods 속성 추가)**

![](/assets/img/posts/129/2.png)

#### **구현하는 원리**

**1\. @RestController 정의**

```java
@RestController
public class AARestController { ....}
```

**2\. response 객체를 사용한 출력 형식과 결과 메시지 지정**

```java
// JSON 형식임을 명시함
response.setContentType("application/json");
// HTTP 상태 코드 설정 (200, 404, 500 등)
response.setStatus(200);
```

**3\. key와 value를 쌍으로 갖는 Map 타입 리턴하는 메서드 구성**

**JSON 구조를 표현한 MAP 객체를 리턴한다.**

```java
@RequestMapping(value = "/professor", method = RequestMethod.GET)
public Map<String, Object) getList() { 
	Map<String, Object> map = new HashMap<String, Object>();
    map.put(key,value);
    return map;
}
```
