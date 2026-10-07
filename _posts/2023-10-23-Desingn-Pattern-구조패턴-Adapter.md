---
title: "[Desingn Pattern] 구조패턴 - Adapter"
date: 2023-10-23 09:00:14 +0900
categories: ["Language", "Design Pattern"]
tags: ["adapter ocp srp", "디자인패턴 adapter", "디자인패턴 adapter 백기선", "어댑터 패턴"]
tistory_url: "https://aroma-bok.tistory.com/entry/Desingn-Pattern-%EA%B5%AC%EC%A1%B0%ED%8C%A8%ED%84%B4-Adapter"
---
### **어댑터 패턴 - Adapter Pattern**

-   **서로 다른 인터페이스를 사용할 수 있도록 바꿔줌으로써 기존 코드를 재사용한다.**
-   **기존 코드를 클라이언트가 사용하는 인터페이스의 구현체로 바꿔주는 패턴**
-   **클라이언트가 사용하는 인터페이스를 따르지 않는 기준 코드를 재사용할 수 있게 해 준다.**

**어댑터 패턴은 일상에서도 쉽게 접할 수 있다. 예를 들어, 220V 코드를 110V 콘센트에 꽂을 때 돼지코라고 불리는 어댑터를 이용하여 110V 콘센트를 이용할 수 있게 도와주는 방법과 유사하다.**

![](/assets/img/posts/235/1.png)

*Adapter를 이용하여 서로 다른 인터페이스를 사용가능하도록 해준다.*

* * *

#### **어댑터 (Adapter) 구현 방법**

-   **써드파티에서 제공되는 클래스 거나, 레거시 코드를 사용하여 새로운 코드를 짤 때, 사용할 수 있다.**
-   **즉, 기존의 코드를 건드리지 않고 나의 코드(Adaptee)와 융합한 클래스(Adapter)를 구현하는 것.**

**써드파티에서 제공되는 로그인 기능이 있다고 가정해 보자.**

-   **이름과 비밀번호 정보를 얻을 수 있는 인터페이스**
-   **Target에 해당**

```java
public interface UserDetails {
    String getUserName();
    String getUserPw();
}
```

-   **이름에 해당하는 유저 정보를 얻을 수 있는 인터페이스**
-   **Target에 해당**

```java
public interface UserDetailsService {
    UserDetails loadUser(String userName);
}
```

-   **로그인을 처리하는 핸들러**
-   **Client에 해당**

```java
public class LoginHandler {

    UserDetailsService userDetailsService;

    public LoginHandler(UserDetailsService userDetailsService) {
        this.userDetailsService = userDetailsService;
    }

    public String login(String userName, String pw) {
        UserDetails userDetails = userDetailsService.loadUser(userName);

        if(userDetails.getUserPw().equals(pw)) {
            return userDetails.getUserName();
        }else {
            throw new IllegalArgumentException();
        }
    }
}
```

-   **각 애플리케이션에 맞게 만드는 일반적인 Account**
-   **써드파티에서 제공되는 기능이 아닌 해당 애플리케이션에만 사용하는 용도의 클래스**
-   **Adaptee에 해당**

```java
@Data
public class Account {
    private String name;
    private String pw;
    private String email;
}
```

-   **각 애플리케이션에 맞게 만드는 일반적인 AccountService**
-   **써드파티에서 제공되는 기능이 아닌 해당 애플리케이션에만 사용하는 용도의 클래스**
-   **Adaptee에 해당**

```java
public class AccountService {

    public Account findAccountByUserName(String userName) {
        Account account = new Account();
        account.setName(userName);
        account.setPw(userName);
        account.setEmail(userName);
        
        return account;
    }
    
    public void createNewAccount(Account account) {
        
    }
    
    public void updateAccount(Account account) {
        
    }
}
```

**써드파티에서 제공되는 기능(UserDetails, UserDetailsService)과 우리 애플리케이션 기능 (Account, AccountService) 은 상호호환 되지 않는다.**

**Adapter를 만들어서 상호호환 될 수 있도록 해보자.**

* * *

#### **어댑터 (Adapter) 적용**

**1\. 클라이언트가 사용하는 인터페이스(Target)를 구현하는 어댑터 (Adapter) 클래스 생성**

-   **UserDetails라는 Target을 Adaptee에 해당하는 Account를 사용해서 구현**

```java
public class AccountUserDetails implements UserDetails {
    private Account account;

    public AccountUserDetails(Account account) {
        this.account = account;
    }

    @Override
    public String getUserName() {
        return account.getName();
    }

    @Override
    public String getUserPw() {
        return account.getPw();
    }
}
```

-   **UserDetailsService라는 Target을 Adaptee에 해당하는 AccountService를 사용해서 구현**
-   **AccountService는 UserDetails와 상관없는 Account를 넘겨주기 때문에 이를 다시 UserDetails로 변환해 주는**  
    **Adapter가 필요**

```java
public class AccountUserDetailsService implements UserDetailsService {

    private AccountService accountService;

    public AccountUserDetailsService(AccountService accountService) {
        this.accountService = accountService;
    }

    @Override
    public UserDetails loadUser(String userName) {
        return new AccountUserDetails(accountService.findAccountByUserName(userName));
    }
}
```

**2\. 어댑터(Adapter)를 사용한 클라이언트 코드**

-   **써드파티에서 제공하는 LoginHandler 사용**

```java
    @Test
    void Test1() {
        AccountService accountService = new AccountService();
        UserDetailsService userDetailsService = new AccountUserDetailsService(accountService);

        LoginHandler loginHandler = new LoginHandler(userDetailsService);

        String login = loginHandler.login("Test", "Test");
        System.out.println(login);
    }
```

**어댑터 (Adapter)를 별도의 클래스로 만들면 기존의 코드는 수정하지 않고 사용가능하다.**

* * *

#### **어댑터 (Adapter) 패턴**

**장점**

-   **기존 코드(Adaptee)를 변경하지 않고 원하는 인터페이스(Target) 구현체를 만들어 재사용할 수 있다.**  
    -   **기존 코드를 변경하지 않고, 확장할 수 있다는 점에서 OCP 원칙에 가까운 패턴**
-   **기존 코드가 하던 일과 특정 인터페이스 구현체로 변환하는 작업을 각기 다른 클래스로 분리하여 관리할 수 있다.**  
    -   **각각 하나의 일에 집중할 수 있기에 SRP 원칙에 가까운 패턴**

**단점**

-   **클래스가 많아지고 구조가 복잡해진다.**

* * *

**어댑터 (Adapter) 패턴 없이 구현 가능?**

-   **코드를 수정할 수 있는 경우라면 가능하다.**
-   **Adaptee가 Target 인터페이스를 직접 구현하도록 수정하면 된다.**
-   **기존 코드가 수정되는 단점이 있지만, 별도의 Adapter를 생성하지 않아도 되기에 복잡도는 줄일 수 있다.**
-   **어댑터 패턴을 적용하면 SRP 원칙을 지킬 수 있지만 상황에 따라 적용이 필요하다.**
