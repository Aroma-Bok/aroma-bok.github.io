---
title: "@PathVariable vs @RequestParam vs @RequestBody 그리고 HttpServletRequest , 파라미터 수신 방법 정리"
date: 2022-09-23 12:32:42 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["pathvariable", "pathvariable vs requestparam vs requestbody", "pathvariable vs requestparam vs requestbody vs httpservletrequest", "requestbody", "requestparam", "requestparam pathvariable 차이", "어노테이션 vs httpservletrequest", "파라미터 수신 방법"]
tistory_url: "https://aroma-bok.tistory.com/entry/PathVariable-vs-RequestParam-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%A0%84%EB%8B%AC-%EB%B0%A9%EB%B2%95"
---
**파라미터 전달할 때 사용하는 어노테이션에 대해 잘못 알고 있던 개념을 다시 한번 정리하기 위해 작성했다.**

* * *

#### **@PathVariable** 

-   **URL 경로에서 경로 변수 값을 추출하는 데 사용**
-   **주로 GET 요청에서 사용**

```java
@RestController
@RequestMapping("/api")
public class MyController {

    @GetMapping("/users/{userId}")
    public ResponseEntity<User> getUser(@PathVariable Long userId) {
        // userId는 경로 변수로 추출된 값입니다.
        User user = userService.getUserById(userId);
        return ResponseEntity.ok(user);
    }
}
```

* * *

#### **@RequestParam**

-   **URL의 쿼리 파라미터를 통해 데이터를 전달할 때 자주 사용**
-   **주로 GET 요청에서 사용되지만, HTML <form> 태그를 사용한 POST 요청에는 사용 가능**

```java
@RestController
@RequestMapping("/api")
public class MyController {

    @GetMapping("/products")
    public ResponseEntity<List<Product>> getProducts(@RequestParam(name = "category") String category) {
        // category는 쿼리 파라미터로 추출된 값입니다.
        List<Product> products = productService.getProductsByCategory(category);
        return ResponseEntity.ok(products);
    }
}
```

**@RequestParam을 Post 요청에서 사용하는 경우에는 요청 분문에 포함된 데이터를 URL-encoded 형태로 전달하는 방식이다.**

> **URL-encoded (x-www-form-urlencoded)**  
> **웹에서 데이터를 전송할 때 사용되는 데이터 인코딩 방식 중 하나**  
>   
> **일반적으로 URL은 ASCII 문자 집합에 속하는 문자만 포함할 수 있다. 하지만 공백, 특수 문자, 비 ASCII 문자 등을**  
> **포함해야 할 경우에는 이러한 문자를 URL-encoding 하여 특정 형식으로 변환한다.**  
> **(ex - 공백은 '+'기호나 '%20'으로 치환된다. 그리고 '@', '#'와 같은 특수 문자는 '%' 기호를 이용하여 ASCII 코드로 표현한다.)**  
>   
> **URL-encoded 형태의 데이터는 주로 웹 폼(<form>)을 통해 데이터를 전송하거나,  
> 쿼리 파라미터를 포함하는 URL을 생성할 때 사용된다.**  
>   
> **name=John+Doe&age=30&city=New+York -> '+' 기호는 공백을 나타내는 데 사용되었다.**  
>   
> **웹 폼(<form>)을 사용하면 @RequestParam을 사용하여 데이터를 받아야 한다.  
> ( HttpServletRequest  예외 )**

```html
<form action="/api/users" method="post">
    <input type="text" name="username" value="Alice">
    <input type="email" name="email" value="alice@example.com">
    <input type="submit" value="Submit">
</form>
```

```java
@RestController
@RequestMapping("/api")
public class UserController {

    @PostMapping("/users")
    public String createUser(@RequestParam String username, @RequestParam String email) {
        // 받아온 username과 email을 이용하여 사용자 생성 로직 수행
        return "User created successfully";
    }
}
```

**location.href로 요청 보낼 때**

-   **주로 GET 요청이 사용되며 URL 파라미터를 통해 데이터를 전송하므로 @RequestParam를 사용**

```javascript
var username = "Alice";
var email = "alice@example.com";
location.href = "/api/users?username=" + encodeURIComponent(username) + "&email=" + encodeURIComponent(email);
```

```java
@GetMapping("/api/users")
public User getUserInfo(@RequestParam("username") String username, @RequestParam("email") String email) {
    // ...
}
```

**주의사항**

-   **@RequestParam의 required 속성은 default가 true 이므로, 값이 전달되지 않으면 예외 발생**
-   **default를 false로 두거나, defaultValue 속성을 사용하여 처리**

```java
@RestController
@RequestMapping("/api")
public class UserController {

    @GetMapping("/user")
    public ResponseEntity<String> getUserInfo(
        @RequestParam(name = "username", required = true) String username,
        @RequestParam(name = "age", required = false, defaultValue = "0") int age
    ) {
        // 받아온 파라미터 사용
        String message = "Username: " + username + ", Age: " + age;
        return ResponseEntity.ok(message);
    }
}
```

* * *

#### **@RequestBody** 

-   **요청 본문(Body)에 들어있는 데이터를 객체로 변환하는 데 사용**
-   **주로 JSON으로 넘어온 데이터를 받을 때 사용**
-   **주로 POST나 PUT 요청의 본문(Body)에 데이터를 담아 보낼 때 사용**

```java
@RestController
@RequestMapping("/api")
public class MyController {

    @PostMapping("/create")
    public ResponseEntity<Product> createProduct(@RequestBody Product product) {
        // product는 요청 본문의 데이터로 변환된 객체입니다.
        productService.createProduct(product);
        return ResponseEntity.ok(product);
    }
}
```

* * *

#### **HttpServletRequest**

-   **클라이언트의 HTTP 요청 정보를 처리하는 객체**
-   **GET, POST 상관없이 요청 파라미터, 헤더, 본문 등에 정보를 접근할 수 있다.**
-   **@PathVariable, @RequestParam, @RequestBody와 달리 좀 더 저수준의 접근 허용**

```java
@Controller
public class ExampleController {

    @RequestMapping(value = "/example", method = {RequestMethod.GET, RequestMethod.POST})
    public String handleRequest(HttpServletRequest request) {
        String param1 = request.getParameter("param1");
        String param2 = request.getParameter("param2");
        
        // param1과 param2를 이용한 로직 처리
        
        return "result"; // 결과 페이지로 이동
    }
}
```

* * *

**@PathVariable은 GET 요청의 경로 변수 추출에 주로 사용**

**@RequestParam은 GET 요청의 쿼리 파라미터 추출에 주로 사용**

**@RequestBody는 POST 요청 본문(Body) 데이터를 처리할 때 사용**

**HttpServletRequest는 GET, POST 요청에 대해서 다 사용 가능**

> **주관적으로는 가독성이나 보안 그리고 유지보수에 있어서 HttpServletRequest 보다는 어노테이션을 사용하는 게 좋다고 생각한다.**
