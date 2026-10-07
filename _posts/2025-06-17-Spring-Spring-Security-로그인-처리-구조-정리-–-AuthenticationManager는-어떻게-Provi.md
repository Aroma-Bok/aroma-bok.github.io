---
title: "[Spring] Spring Security 로그인 처리 구조 정리 – AuthenticationManager는 어떻게 Provider를 호출할까?"
date: 2025-06-17 14:44:13 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["authenticationmanager", "spring security 로그인 처리 구조", "usernamepasswordauthenticationtoken", "스프링 시큐리티 로그인 방식", "스프링시큐리티 로그인 구조"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Security-%EB%A1%9C%EA%B7%B8%EC%9D%B8-%EC%B2%98%EB%A6%AC-%EA%B5%AC%EC%A1%B0-%EC%A0%95%EB%A6%AC-%E2%80%93-AuthenticationManager%EB%8A%94-%EC%96%B4%EB%96%BB%EA%B2%8C-Provider%EB%A5%BC-%ED%98%B8%EC%B6%9C%ED%95%A0%EA%B9%8C"
---
**Spring Security를 사용하면서 직접 \`AuthenticationProvider\`를 구현했는데, 문득 궁금해졌다.**

**UsernamePasswordAuthenticationToken을 넘기면 어떻게 내가 만든 Provider가 호출되는 거지?**

**이 궁금증을 해결한 과정을 정리한다.**

* * *

#### **1.Spring Security 로그인 방식 개념**

-   **Spring Security는 Spring 기반 애플리케이션의 \*\*인증(Authentication)\*\*과 \*\*인가(Authorization)\*\*를 위한  
    보안 프레임워크**
-   **로그인 과정은 인증(Authentication) 단계이며, 사용자의 신원을 확인하는 것이 목적**

#### **2.로그인 처리의 기본 흐름**

-   **Spring Security는 기본적으로 UsernamePasswordAuthenticationFilter를 통해 로그인 요청을 처리**

**동작 순서**

1.  **클라이언트 로그인 요청**  
    **\-POST /login 요청**  
    **\-username, password 파라미터 포함 (기본값)**
2.  **UsernamePasswordAuthenticationFilter 동작**  
    **\-요청을 가로채서 Authentication 객체 생성**  
    **\-이 객체를 AuthenticationManager로 전달**
3.  **AuthenticationManager 처리**  
    **\-실제 인증 로직을 위해 UserDetailsService 호출**
4.  **UserDetailsService**  
    **\-사용자를 찾고(loadUserByUsername)**  
    **\-UserDetails 객체 반환 (비밀번호 등 포함)**
5.  **PasswordEncoder 비교**  
    **\-입력한 비밀번호와 저장된 비밀번호 비교**
6.  **성공/실패 처리**  
    **\-성공: SecurityContext에 인증 정보 저장**  
    **\-실패: 로그인 실패 응답**
7.  **이후 요청 시**  
    **\-세션 혹은 JWT 등으로 인증 상태 유지**

#### **3\. 핵심 구성 요소**

<table style="border-collapse: collapse; width: 100%; height: 147px;" border="1" data-ke-align="alignCenter"><tbody><tr style="height: 21px;"><td style="width: 50.5814%; height: 21px; text-align: left;"><b><span style="font-family: 'Noto Serif KR';">UsernamePasswordAuthenticationFilter</span></b></td><td style="width: 49.4186%; height: 21px; text-align: left;"><b><span style="font-family: 'Noto Serif KR';">로그인 요청을 가로채 처리</span></b></td></tr><tr style="height: 21px;"><td style="width: 50.5814%; height: 21px; text-align: left;"><b><span style="font-family: 'Noto Serif KR';">AuthenticationManager</span></b></td><td style="width: 49.4186%; height: 21px; text-align: left;"><b><span style="font-family: 'Noto Serif KR';">인증을&nbsp;총괄</span></b></td></tr><tr style="height: 21px;"><td style="width: 50.5814%; height: 21px; text-align: left;"><b><span style="font-family: 'Noto Serif KR';">UserDetailsService</span></b></td><td style="width: 49.4186%; height: 21px; text-align: left;"><b><span style="font-family: 'Noto Serif KR';">사용자&nbsp;정보&nbsp;조회</span></b></td></tr><tr style="height: 21px;"><td style="width: 50.5814%; height: 21px; text-align: left;"><b><span style="color: #333333; text-align: left; font-family: 'Noto Serif KR';">UserDetails&nbsp;</span></b></td><td style="width: 49.4186%; height: 21px; text-align: left;"><b><span style="font-family: 'Noto Serif KR';">사용자&nbsp;상세&nbsp;정보&nbsp;인터페이스</span></b></td></tr><tr style="height: 21px;"><td style="width: 50.5814%; height: 21px; text-align: left;"><b><span style="font-family: 'Noto Serif KR';">PasswordEncoder</span></b></td><td style="width: 49.4186%; height: 21px; text-align: left;"><b><span style="font-family: 'Noto Serif KR';">비밀번호 암호화 및 비교</span></b></td></tr><tr style="height: 21px;"><td style="width: 50.5814%; height: 21px; text-align: left;"><b><span style="color: #333333; text-align: left; font-family: 'Noto Serif KR';">SecurityContextHolder&nbsp;</span></b></td><td style="width: 49.4186%; height: 21px; text-align: left;"><b><span style="font-family: 'Noto Serif KR';">현재 인증 정보 보관소</span></b></td></tr><tr style="height: 21px;"><td style="width: 50.5814%; height: 21px; text-align: left;"><b><span style="color: #333333; text-align: left; font-family: 'Noto Serif KR';">SecurityFilterChain&nbsp;</span></b></td><td style="width: 49.4186%; height: 21px; text-align: left;"><b><span style="font-family: 'Noto Serif KR';">전체&nbsp;필터&nbsp;체인&nbsp;설정</span></b></td></tr></tbody></table>

**위 내용은 기본적인 Spring Security의 로그인 개념과 기본 흐름이다.**

**아래는 내가 만든 소스가 기본적인 구조와 비교하여 차이점을 비교해 봤다.**

* * *

#### **Spring Security 로그인 처리 기본 구조 요약**

1.  **클라이언트가 로그인 요청을 보낸다 (\`/login\`)**
2.  **\`UsernamePasswordAuthenticationFilter\`가 요청을 가로챈다**
3.  **토큰을 생성해 \`AuthenticationManager\`에게 전달**
4.  **\`AuthenticationManager\`는 적절한 \`AuthenticationProvider\`를 선택**
5.  **인증 성공 시 \`SecurityContextHolder\`에 저장**

**그렇다면,** **\`AuthenticationManager\`는 어떤 기준으로 내가 만든 \`AuthenticationProvider\`를 호출할까?**

**바로 \`supports()\` 메서드가 그 핵심이다.**

**일단 이 순서를 코드에서 진행되는 순서는 이렇다.**

```java
// 토큰 생성
UsernamePasswordAuthenticationToken authenticationToken = new UsernamePasswordAuthenticationToken(username, password);

// 인증 요청 → 이 시점에 Provider가 호출된다
Authentication authentication = authenticationManager.authenticate(authenticationToken);

**authenticationManager.authenticate(authenticationToken) 호출이 바로 **CustomAuthenticationProvider**를 실행하는 트리거 포인트
```

**configure(AuthenticationManagerBuilder)에서 내가 명시적으로 provider 등록했다.**

**그래서 authenticate()가 호출될 때 Spring Security는 내부적으로 이 provider를 찾아 실행**

**AuthenticationManager는 등록된 AuthenticationProvider 목록을 돌면서 supports(...) 호출**

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.userDetailsService(userDetailsService).passwordEncoder(passwordEncoder);
    auth.authenticationProvider(new CustomAuthenticationProvider(userDetailsService,passwordEncoder, lgnMapper));
}
```

```java
// CustomAuthenticationProvider.java
@Override
public boolean supports(Class<?> authentication) {
    return UsernamePasswordAuthenticationToken.class.isAssignableFrom(authentication);
}
```

**true 반환됨 → 이 provider가 선택되어 authenticate() 호출된다.**

**authenticate()로 들어온 Authentication 객체가**

**UsernamePasswordAuthenticationToken이거나, 그 하위 클래스이면 지원한다는 의미이다.**

* * *

#### **설명 요약**

**AuthenticationManager는 등록된 AuthenticationProvider들 중 supports()가 true인 걸 찾아 authenticate()를 호출한다.**

**내가 만든 Provider에서 UsernamePasswordAuthenticationToken을 지원한다고 했기 때문에,**

**내가 만든 로직이 실행된다.**

**Spring Security에서 AuthenticationManager가 AuthenticationProvider 중 어떤 걸 호출할지 판단할 때 핵심 기준은 supports()** **메서드이다.**

> **supports()를 잘못 구현하면 Provider가 호출되지 않는다.  
> **  
> **커스텀 Provider는 여러 인증 방식(예: JWT, 소셜 로그인 등)을 동시에 처리할 때도 사용된다.**  
> **\`UsernamePasswordAuthenticationToken\`은 인증 전/후에 모두 사용되며, 생성자 사용 방식이 다르다.  
> **  
> **\`authenticationManager.authenticate(token)\`이 내가 만든 인증 로직을 실행하는 이유는,**  
> **내 Provider가 해당 토큰을 지원(\`supports\`)한다고 명시했기 때문이다.**  
> **Spring Security는 생각보다 명확하고, 우리가 규칙만 지키면 유연하게 확장할 수 있다.**
