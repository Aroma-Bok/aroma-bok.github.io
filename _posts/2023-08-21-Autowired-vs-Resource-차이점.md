---
title: "@Autowired vs @Resource 차이점"
date: 2023-08-21 15:24:00 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["@autowired @resource 차이점"]
tistory_url: "https://aroma-bok.tistory.com/entry/Autowired-vs-Resource-%EC%B0%A8%EC%9D%B4%EC%A0%90"
---
**보통 @Autowired를 활용해서 의존성 주입을 해왔지만 @Resource를 이용해서 의존성 주입하는 프로젝트를 진행하면서**

**차이점을 공부하려고 정리해 본다.**

* * *

**@Autowired와 @Resource는 빈(Bean)을 주입하는 데 사용되는 어노테이션이다.**

**둘 다 빈(Bean) 주입을 위한 목적으로 사용되지만, 몇 가지 차이점이 있다.**

#### **@Autowired**

-   **스프링의 의존성 주입(DI) 기능을 활용하여 빈(Bean)을 주입**
-   **타입(Type)에 따라 주입을 수행하며, 동일한 타입의 빈(Bean)이 여러 개 존재할 경우에는 변수 이름과 빈(Bean)의 이름을 비교하여 주입**
-   **생성자, 필드, 메서드의 파라미터에 어노테이션을 사용하여 의존성을 주입할 수 있다.**
-   **Spring에서 제공하는 어노테이션**

**필드 주입**

```java
@Service
public class UserService {
    // @Autowired를 사용하여 UserServiceImpl 빈을 주입합니다.
    @Autowired
    private UserRepository userRepository;
    // ...
}
```

**생성자 주입**

```java
@Service
public class UserService {
    private final UserRepository userRepository;

    @Autowired
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    // ...
}
```

**메서드 주입**

```java
@Service
public class UserService {
    private UserRepository userRepository;

    @Autowired
    public void setUserRepository(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    // ...
}
```

**@Qualifier 어노테이션 사용 예시 - 동일한 타입의 빈이 여러 개인 경우**

**각 빈(Bean)의 @Component, @Service, @Repository 등의 어노테이션에서 설정한 이름 이거나 빈(Bean) 이름을 명시적으로 지정한 경우에 해당한다.**

```java
@Service
public class UserService {
    @Autowired
    @Qualifier("customUserRepository") // customUserRepository 빈을 주입
    private UserRepository userRepository;
    // ...
}
```

* * *

#### **@Resource**

-   **Java 표준 어노테이션으로, Spring과 별개로 Java EE와 호환되는 어노테이션**
-   **주로 이름(Name)에 따라 빈(Bean)을 주입한다. 기본적으로 빈(Bean)의 이름과 변수 이름을 비교하여 주입하며, 'name' 속성을 사용하여 빈(Bean)의 이름을 직접 지정할 수 있다.**
-   **필드, 메서드에 사용할 수 있으며, 생성자에는 사용할 수 없다.**

**필드 주입**

```java
@Service
public class UserService {
    // @Resource를 사용하여 userServiceImpl 빈을 주입합니다.
    @Resource
    private UserServiceImpl userServiceImpl;
    // ...
}
```

**메서드 주입**

```java
@Service
public class UserService {
    private UserServiceImpl userServiceImpl;

    @Resource
    public void setUserServiceImpl(UserServiceImpl userServiceImpl) {
        this.userServiceImpl = userServiceImpl;
    }
    // ...
}
```

**이름지정**

```java
@Service
public class UserService {
    // @Resource의 name 속성을 사용하여 다른 이름의 빈을 주입합니다.
    @Resource(name = "customUserService")
    private UserService customUserService;
    // ...
}
```

* * *

**즉, @Autowired는 타입(Type)에 따라 주입하고, 변수 이름과 빈(Bean)의 이름을 비교한다.**

**@Resource는 이름(Name)에 따라 주입하며, 변수 이름과 빈(Bean)의 이름을 비교하거나 'name' 속성을 통해 지정한다.**
