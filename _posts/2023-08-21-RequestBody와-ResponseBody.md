---
title: "@RequestBody와 @ResponseBody"
date: 2023-08-21 14:32:49 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["@requestbody", "@responsebody"]
tistory_url: "https://aroma-bok.tistory.com/entry/RequestBody%EC%99%80-ResponseBody"
---
**@RequestBody와 @ResponseBody**

**Spring에서 사용되는 어노테이션으로, HTTP 요청의 본문(Body) 데이터와 HTTP 응답의 본문(Body) 데이터를 처리하는 데 사용된다. 주로 RESTful 웹 서비스에서 JSON이나 XML과 같은 데이터 형식을 전송하고 수신할 때 활용된다.**

* * *

#### **@RequestBody**

**HTTP 요청의 본문(Body) 데이터를 특정 Java 객체로 변환하는 데 사용된다. 주로 POST, PUT 요청에서 클라이언트가 전송한 데이터를 서버에서 파싱 하여 Java 객체로 변환한다.**

```java
@PostMapping("/create")
public ResponseEntity<User> createUser(@RequestBody User user) {
    // user 객체 활용
}
```

#### **@ResponseBody**

**Controller 메서드가 반환하는 Java 객체를 HTTP 응답의 본문(Body) 데이터로 변환하여 클라이언트에게 전달하는 데 사용된다. 주로 JSON이나 XML 형식으로 데이터를 반환할 때 사용한다.**

```java
@GetMapping("/user/{id}")
@ResponseBody
public User getUser(@PathVariable Long id) {
    User user = userService.getUserById(id);
    return user;
}
```

**두 어노테이션 모두 Spring의 메시지 컨버터(Message Converter) 기능을 활용하여 자동으로 데이터 변환을 처리한다.**

> **\*메시지 컨버터(Message Converter)   
> HTTP 요청과 응답의 Body 데이터를 다양한 형식으로 변환하는 역할을 한다.**  
> ****이를 통해 클라이언트와 서버 간의 데이터 교환을 다양한 데이터 형식(Json, XML, HTML 등)으로  
> 처리할 수 있게 해 준다.  
>   
> ****
> 
> **작업 순서는 변환 -> 자동 타입 매핑 -> 자동선택으로 수행한다.**
> 
> **JSON과 XML 같은 데이터 형식을 처리하는 Jackson, Gson, JAXB 같은 라이브러리들은 스프링에서 자주 사용되는  
> 메시지 컨버터 예시이다.**

**즉, @RequestBody는 클라이언트의 데이터를 Java 객체로 변환하고, @ResponseBody는 Java 객체를 클라이언트가 원하는 형식(JSON, XML 등)으로 변환하여 전송한다.**
