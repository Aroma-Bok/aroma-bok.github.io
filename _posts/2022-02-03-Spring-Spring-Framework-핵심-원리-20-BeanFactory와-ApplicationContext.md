---
title: "[Spring] Spring Framework - 핵심 원리 (20) - BeanFactory와 ApplicationContext"
date: 2022-02-03 23:27:50 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["applicationcontext", "beanfactory", "스프링 컨테이너"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-20-BeanFactory%EC%99%80-ApplicationContext"
---
### **BeanFactory와 ApplicationContext**

* * *

![](/assets/img/posts/82/1.png)

*맨 밑에는 구현클래스*

#### **BeanFactory**

-   **스프링 컨테이너의 최상위 인터페이스**
-   **스프링 빈을 관리하고 조회하는 역할을 담당**
-   **getBean()을 제공**
-   **지금까지 우리가 사용했던 대부분의 기능은 BeanFactory가 제공하는 기능**

#### **ApplicationContext**

-   **BeanFactory 기능을 모두 상속받아서 제공**

**빈을 관리하고 검색하는 기능을 BeanFactory가 제공해주는데, 그러면 둘의 차이는?**

**\=> 애플리케이션을 개발할 때는 빈은 관리하고 조회하는 기능은 물론이고, 수많은 부가기능이 필요**

**즉, 다양한 부가기능까지 사용하기 위해서 ApplicationContext를 사용한다.**

**ApplicationContext의 API를 찾아서 들어가 보면 아래처럼 상속을 많이 받아있는 것을 볼 수 있다.**

```java
public interface ApplicationContext extends EnvironmentCapable, ListableBeanFactory, HierarchicalBeanFactory,
		MessageSource, ApplicationEventPublisher, ResourcePatternResolver {
```

**그리고 ListableBeanFactory를 들어가면, BeanFacotry를 상속받고 있는 것을 알 수 있다.**

```java
public interface ListableBeanFactory extends BeanFactory {
```

![](/assets/img/posts/82/2.png)

*다양한 인터페이스를 상속 받고있다.*

**메시지 소스를 활용한 국제화 기능**

-   **예를 들어서 한국에서 들어오면 한국어로, 영어권에서 들어오면 영어로 출력**

**환경변수**

-   **로컬, 개발, 운영 등을 구분해서 처리**

**애플리케이션 이벤트**

-   **이벤트를 발행하고 구독하는 모델을 편리하게 지원**

**편리한 리소스 조회**

-   **파일, 클래스 패스, 외부 등에서 리소스를 편리하게 조회**

* * *

### **정리**

-   **ApplicationContext는 BeanFacotry의 기능을 상속받는다.**
-   **ApplicationContext는 빈 관리 기능 + 편리한 부가 기능을 제공한다.**
-   **BeanFactory를 직접 사용할 일은 거의 없다. 부가기능이 포함된 ApplicationContext를 사용한다.**
-   **BeanFactory나 ApplicationContext를 스프링 컨테이너라 한다.**
