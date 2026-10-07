---
title: "REST? RESTful? 개념 정리와 코드를 통한 예시"
date: 2023-08-31 10:21:22 +0900
categories: ["Solution & Tools", "Web"]
tags: ["rest", "rest 예시", "restful api"]
tistory_url: "https://aroma-bok.tistory.com/entry/REST-RESTful-%EA%B0%9C%EB%85%90-%EC%A0%95%EB%A6%AC%EC%99%80-%EC%BD%94%EB%93%9C%EB%A5%BC-%ED%86%B5%ED%95%9C-%EC%98%88%EC%8B%9C"
---
#### **REST**

**Representational State Transfer의 약자로, 웹 기반의 아키텍처 스타일 중 하나**

**REST는 자원을 고유한 URI로 표현하고 HTTP 메서드를 통해 자원에 대한 CRUD 동작을 수행하는 아키텍처 스타일**

#### **RESTful API**

**REST 아키텍처 원칙을 따르는 API**

**특징**

1.  **자원 (Resource) : 웹 리소스를 나타내는 개념으로, URI로 식별**  
    **예를 들어, "/users"는 사용자 리소스, "/products"는 제품 리소스를 나타낸다.**
2.  **HTTP 메서드 : CRUD 작업을 나타내는 HTTP 메서드 사용**  
    **GET - 조회 | POST - 생성 | PUT - 갱신 | DELETE - 삭제**
3.  **Representations : 리소스의 표현 형태로 JSON, XML 등이 사용**

* * *

**GET - 좋은 예**

```java
// 리소스 가져오기
@GetMapping("/api/users/{id}")
public ResponseEntity<User> getUser(@PathVariable Long id) {
    User user = userRepository.findById(id).orElse(null);
    if (user != null) {
        return ResponseEntity.ok(user);
    } else {
        return ResponseEntity.notFound().build();
    }
}
```

**POST - 좋은 예**

```java
// 리소스 생성
@PostMapping("/api/users")
public ResponseEntity<User> createUser(@RequestBody User newUser) {
    User savedUser = userRepository.save(newUser);
    return ResponseEntity.status(HttpStatus.CREATED).body(savedUser);
}
```

**PUT - 좋은 예**

```java
// 리소스 업데이트
@PutMapping("/api/users/{id}")
public ResponseEntity<User> updateUser(@PathVariable Long id, @RequestBody User updatedUser) {
    User user = userRepository.findById(id).orElse(null);
    if (user != null) {
        updatedUser.setId(id);
        User savedUser = userRepository.save(updatedUser);
        return ResponseEntity.ok(savedUser);
    } else {
        return ResponseEntity.notFound().build();
    }
}
```

**DELETE - 좋은 예**

```java
// 리소스 삭제
@DeleteMapping("/api/users/{id}")
public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
    userRepository.deleteById(id);
    return ResponseEntity.noContent().build();
}
```

* * *

**GET - 나쁜 예**

```java
// 경로 변수가 아닌 요청 파라미터로 사용
@GetMapping("/api/users")
public ResponseEntity<User> getUserById(@RequestParam Long id) {
    User user = userRepository.findById(id).orElse(null);
    if (user != null) {
        return ResponseEntity.ok(user);
    } else {
        return ResponseEntity.notFound().build();
    }
}
```

**POST - 나쁜 예**

```java
// 경로 변수가 아닌 요청 파라미터로 사용
@PostMapping("/api/users")
public ResponseEntity<User> createUser(@RequestParam String username, @RequestParam String email) {
    User newUser = new User(username, email);
    User savedUser = userRepository.save(newUser);
    return ResponseEntity.status(HttpStatus.CREATED).body(savedUser);
}
```

**PUT - 나쁜 예**

```java
// 경로 변수가 아닌 요청 파라미터로 사용
@PutMapping("/api/users")
public ResponseEntity<User> updateUser(@RequestParam Long id, @RequestParam String username, @RequestParam String email) {
    User user = userRepository.findById(id).orElse(null);
    if (user != null) {
        user.setUsername(username);
        user.setEmail(email);
        User savedUser = userRepository.save(user);
        return ResponseEntity.ok(savedUser);
    } else {
        return ResponseEntity.notFound().build();
    }
}
```

**DELETE - 나쁜 예**

```java
// 경로 변수가 아닌 요청 파라미터로 사용
@DeleteMapping("/api/users")
public ResponseEntity<Void> deleteUser(@RequestParam Long id) {
    userRepository.deleteById(id);
    return ResponseEntity.noContent().build();
}
```

* * *

**좋은 예는 RESTful 한 API 디자인을 원칙을 따르고 있다.**

**나쁜 예는 경로 변수 대신 요청 파라미터를 사용하거나, 리소스 식별이 모호한 경우이다.**

**RESTful 한 API를 디자인할 때는 URL 패턴, HTTP 메서드, 요청 본문 등을 명확하게 사용하여**

**리소스의 조회, 생성, 갱신, 삭제와 같은 동작을 명확하게 나타내는 것이 중요하다.**
