---
title: "[Spring Security, Thymeleaf] #authentication, #authorization 인증 객체 (헬퍼 객체)"
date: 2024-10-10 14:35:37 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["#authentication", "#authorization", "spring security", "thymeleaf", "타임리프 인증객체", "타임리프 헬퍼 객체"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Security-Thymeleaf-authentication-authorization-%EC%9D%B8%EC%A6%9D-%EA%B0%9D%EC%B2%B4-%ED%97%AC%ED%8D%BC-%EA%B0%9D%EC%B2%B4"
---
#### **일반적으로 스프링 시큐리티 인증을 받은 로그인한 사용자의 정보를 타임리프에서 사용하고 싶을 때 사용**

* * *

**Spring Security와 Thymeleaf는 자연스럽게 통합되어 인증 및 권한 정보를 템플릿에서 쉽게**

**접근할 수 있도록 지원**  
  
**Spring Security의 인증정보를 Thymeleaf 템플릿 내에서 접근할 때는 #authentication 또는** **#authorization이라는 헬퍼 객체를 사용**

* * *

### **#authentication 헬퍼 객체**

-   **#authentication은 현재 인증된 사용자의 정보를 가져오는 데 사용**
-   **Spring Security의 Authentication 객체에 있는 다양한 정보를 조회**

#### **주요 속성**

**#authentication.name**  
**: 현재 로그인한 사용자의 이름**  
**#authentication.principal**  
**: 인증된 사용자의 주체**  
**#authentication.authorities**  
**: 사용자가 가진 권한**  
  

```html
<p>Username: [[${#authentication.name}]]</p>
<p>Authorities: [[${#authentication.authorities}]]</p>
```

* * *

### **#authorization 헬퍼 객체**

-   **#authorization은 현재 사용자가 특정 권한이나 역할을 가지고 있는지를 검사하는데 유용**
-   **주로 조건문에서 사용**

```html
<p sec:authorize="hasRole('ROLE_ADMIN')">This is visible to admins only.</p>
```

* * *

### **#authentication.principal을 통한 사용자 정보 접근**

-   **Spring Security에서 Authentication 객체의 principal은 UserDetails 객체를 가리킨다**
-   **커스텀 사용자 정보를 UserDetail에 추가하고 싶다면, 해당 정보를 principal을 통해 접근**

```java
public class CustomUserDetails implements UserDetails {
    private String username;
    private String userNm;  // 커스텀 필드

    public String getUserNm() {
        return userNm;
    }

    @Override
    public String getUsername() {
        return username;
    }

    // 나머지 메서드들...
}
```

```html
<p>User Full Name: [[${#authentication.principal.userNm}]]</p>
```

* * *

**Thymeleaf에서 Spring Security의 인증 객체를 표현식으로 사용하려면 #authentication 및**  
**#authorization 헬퍼 객체를 사용하면 된다.**

**이를 통해 인증된 사용자 정보에 접근하고, 권한에 따라 UI를 제어할 수 있다.**
