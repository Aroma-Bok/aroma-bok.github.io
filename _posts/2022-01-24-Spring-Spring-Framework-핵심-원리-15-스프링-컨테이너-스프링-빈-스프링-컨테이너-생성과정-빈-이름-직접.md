---
title: "[Spring] Spring Framework - 핵심 원리 (15) - 스프링 컨테이너 + 스프링 빈 + 스프링 컨테이너 생성과정 + 빈 이름 직접 부여"
date: 2022-01-24 23:19:29 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["빈 이름 직접 부여", "스프링 빈", "스프링 컨테이너 생성과정", "의존관계 주입"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-15-%EC%8A%A4%ED%94%84%EB%A7%81-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B9%88-%EC%8A%A4%ED%94%84%EB%A7%81-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EC%83%9D%EC%84%B1%EA%B3%BC%EC%A0%95-%EB%B9%88-%EC%9D%B4%EB%A6%84-%EC%A7%81%EC%A0%91-%EB%B6%80%EC%97%AC"
---
## **스프링 컨테이너 + 스프링 빈 + 스프링 컨테이너 생성과정 + 빈 이름 직접 부여**

* * *

**이제부터는 스프링 그 자체에 대해서 알아보자.**

### **1\. 스프링 컨테이너 생성**

```java
ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
```

-   **'ApplicationContext'를 '스프링 컨테이너'라 한다.**
-   **'ApplicationContext'는 ''인터페이스'라 한다. 즉, '다형성'이 적용이 되어있다.**
-   **'스프링 컨테이너'는 'XML'을 기반으로 만들 수 있고, '어노테이션' 기반의 자바 설정 클래스로 만들 수 있다.**  
    **\=> '@Configuration' & '@Bean'**
-   **직전에 'AppConfig'를 사용했던 방식이 '어노테이션' 기반의 설정 클래스로 '스프링 컨테이너'를 만든 것이다.**  
    **\=> 요즘엔 '어노테이션' 기반으로 많이 사용한다.**
-   **자바 설정 클래스를 기반으로 '컨테이너(ApplicationContext)'를 만들어보자.**  
    **\=> 'AnnotationConfigApplicationContext'는 'ApplicationContext'의 구현체이다.**

> **참고 : 더 정확히는 '스프링 컨테이너'를 부를 때, 'BeanFactory', 'ApplicationContext'로 구분해서 이야기한다.**  
> **이 부분은 뒤에서 설명하겠다. 'BeanFactory'를 직접 사용하는 경우는 거의 없으므로 일반적으로 'ApplicationContext'를 '스프링 컨테이너'라 한다.**

* * *

## **스프링 컨테이너 생성 과정**

#### **1\. 스프링 컨테이너 생성**

![](/assets/img/posts/76/1.png)

**1\. new AnnotationConfigApplicationContext(AppConfig.class)**

**2\. '스프링 컨테이너'를 생성할 때는 구성 정보를 지정해주어야 한다.**

**3\. 여기서는 'AppConfig.class'를 구성 정보로 지정했다.**

#### **2\. 스프링 빈 등록**

![](/assets/img/posts/76/2.png)

*Key : Value*

**1\. '스프링 컨테이너'는 파라미터로 넘어온 설정 클래스 정보를 사용해서 '스프링 빈'을 등록한다.**

**빈 이름**

**'빈(Bean) 이름'은 '메서드 이름'을 사용한다.**

**'빈(Bean) 이름'을 직접 부여할 수 도 있다.**

**E.g : @Bean(name="memberService2")**

> **주의 : 빈 이름은 항상 다른 이름을 부여해야 한다. 같은 이름을 부여하면, 다른 빈이 무시되거나,**  
> **기존 빈을 덮어버리거나 설정에 따라 오류가 발생한다.**

#### **3\. 스프링 빈 의존관계 설정 - 준비**

![](/assets/img/posts/76/3.png)

#### **4\. 스프링 빈 의존관계 설정 - 완료**

![](/assets/img/posts/76/4.png)

**1\. '스프링 컨테이너'는 설정 정보를 참고해서 '의존관계를 주입(DI)'한다.**

**2\. 단순히 자바 코드를 호출하는 것 같지만, 차이가 있다. 이 차이는 뒤에 '싱글톤 컨테이너'에서 설명하겠다.**

> **참고 : 스프링 빈을 생성하고, 의존관계를 주입하는 단계가 나누어져 있다.**  
> **그런데 이렇게 자바 코드로 스프링 빈을 등록하면서 생성자를 호출하면서 의존관계 주입도 한 번에 처리된다.**  
> **('memberService'를 생성하면서 'memberRepository'를 호출한다. 즉, 호출하는 순간 '의존관계'가 엮인다.)**  
> **여기서는 이해를 돕기 위해 개념적으로 나누어 설명했다. 자세한 내용은 '의존관계 자동 주입'에서 다시**  
> **설명하겠다.**

### **정리**

**스프링 컨테이너를 생성하고, 설정(구성) 정보를 참고해서 스프링 빈도 등록하고, 의존 관계도 설정했다.**

**이제 스프링 컨테이너에서 데이터를 조회해보자.**
