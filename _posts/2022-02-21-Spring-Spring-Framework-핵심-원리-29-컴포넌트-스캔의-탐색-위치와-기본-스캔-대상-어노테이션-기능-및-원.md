---
title: "[Spring] Spring Framework - 핵심 원리 (29) - 컴포넌트 스캔의 탐색 위치와 기본 스캔 대상 + 어노테이션 기능 및 원리 + basePackageClasses + basePackages + SpringBoot 원리"
date: 2022-02-21 21:06:43 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["@componenetscan", "@componenetscan 탐색 원리", "@controller", "@service", "basepackageclasses", "basepackages", "springboot 원리", "어노테이션 기능", "어노테이션 용도", "어노테이션 중요점"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-29-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8-%EC%8A%A4%EC%BA%94%EC%9D%98-%ED%83%90%EC%83%89-%EC%9C%84%EC%B9%98%EC%99%80-%EA%B8%B0%EB%B3%B8-%EC%8A%A4%EC%BA%94-%EB%8C%80%EC%83%81-%EC%96%B4%EB%85%B8%ED%85%8C%EC%9D%B4%EC%85%98-%EA%B8%B0%EB%8A%A5-%EB%B0%8F-%EC%9B%90%EB%A6%AC-basePackageClasses-basePackages-SpringBoot-%EC%9B%90%EB%A6%AC"
---
### **컴포넌트 스캔의 탐색 위치와 기본 스캔 대상 + 어노테이션 기능 및 원리 + basePackageClasses + basePackages + SpringBoot 원리** 

* * *

#### **탐색 위치와 기본 스캔 대상**

**탐색할 패키지의 시작 위치 지정**

**모든 자바 클래스를 다 컴포넌트 스캔하면 시간이 오래 걸린다.**

**그래서 꼭 필요한 위치부터 탐색하도록 시작 위치를 지정할 수 있다.**

**AutoAppConfig에 basePackages = "시작 위치 ex) hello.core.member"만 추가해주면 된다.**

```java
@ComponentScan(
		basePackages = "hello.core.member",
```

**결과를 보면 지정된 위치에서 검색된 빈만 컴포넌트 스캔을 했다.**

```java
20:20:05.392 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'autoAppConfig'
20:20:05.403 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'memberServiceImpl'
20:20:05.438 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'memoryMemberRepository'
```

-   **basePacekages : 탐색할 패키지의 시작 위치를 지정한다. 이 패키지를 포함해서 하위 패키지를 모두 탐색한다.**  
    **※ 만약 없으면? 모든 Java코드를 다 뒤진다고 생각해보자. (Library 포함) 너무 많은 코드를 검색한다.**
-   **basePackages = {"hello.core", "hello.service"} 이렇게 여러 시작 위치를 지정할 수도 있다.**
-   **basePackageClasses : 지정한 클래스의 패키지를 탐색 시작 위치로 지정한다.**  
    **\=> 아래처럼 지정하면 해당 클래스의 패키지에서 탐색 시작 위치가 지정된다.**
-   basePackageClasses = AutoAppConfig.class,
-   **만약 지정하지 않으면 @ComponentScan이 붙은 설정 정보 클래스의 패키지가 시작 위치가 된다.**

> **권장하는 방법**  
> **패키지 위치를 지정하지 않고, 설정 정보 클래스의 위치를 프로젝트 최상단에 두는 것이다.**  
> **최근 스프링 부트도 이 방법을 기본으로 제공한다.**

![](/assets/img/posts/96/1.png)

**com.hello -> 프로젝트 시작 루트, 여기에 AppConfig 같은 메인 설정 정보를 두고,**

**@ComponenetScan 어노테이션을 붙이고, basePackages 지정은 생략한다.**

**이렇게 하면, com.hello를 포함한 하위는 모두 자동으로 컴포넌트 스캔의 대상이 된다.**

**그리고 프로젝트 메인 설정 정보는 프로젝트를 대표하는 정보이기 때문에 프로젝트 시작 루트 위치에 두는 것이 좋다고 생각한다.**

**참고로 스프링 부트를 사용하면 스프링 부트의 대표 시작 정보인 @SprigBootApplication를**

**이 프로젝트 시작 루트 위치에 두는 것이 관례이다. (이 설정 안에 바로 @ComponentSca이 들어있다.)**

* * *

**아래는 프로젝트를 처음 만들면 자동으로 만들어지는 CoreApplication 코드이다. @SpringBootApplication을 확인해보면 @ComponentScan이 있는 것을 확인할 수 있다.**

**그래서 SpringBoot를 사용하면 자동적으로 돌아가는 원리이다.**

```java
package hello.core;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class CoreApplication {

	public static void main(String[] args) {
		SpringApplication.run(CoreApplication.class, args);
	}
}
```

```java
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
```

* * *

**컴포넌트 스캔 기본 대상**

**컴포넌트 스캔은 @Component 뿐만 아니라 다음과 내용도 추가로 대상에 포함한다.**

**@Component : 컴포넌트 스캔에서 사용**

**@Controller : 스프링 MVC 컨트롤러에서 사용**

**@Service : 스프링 비즈니스 로직에서 사용**

**@Repository : 스프링 데이터 접근 계층에서 사용 ex) JPA, JDBC**

**@Configuration : 스프링 설정 정보에서 사용**

**아래 해당 어노테이션을 찾아서 들어가면 아래처럼 @Component가 있는 것을 확인할 수 있다.**

```java
@Component
public @interface Repository {

@Component
public @interface Service {

@Component
public @interface Configuration {
```

**참고 : 메서드 및 인터페이스 찾기 ▶Ctrl + Shift + T**

> **중요**  
> **어노테이션에는 상속관계라는 것이 없다. 그래서 이렇게 어노테이션이 특정 어노테이션을 들고 있는 것을 인식할 수 있는 것은 자바 언어가 지원하는 기능이 아니고, 스프링이 지원하는 기능이다.**

* * *

**컴포넌트 스캔의 용도뿐만 아니라 다음 어노테이션이 있으면 스프링은 부가 기능을 수행한다.**

**@Controller : 스프링 MVC 컨트롤러로 인식**

**@Service : 사실 @Service는 특별한 처리를 하지 않는다. 대신 개발자들이 핵심 비즈니스 로직이**

**여기에 있겠구나라고 비즈니스 계층을 인식하는데 도움이 된다.**

**@Repositroy : 스프링 데이터 접근 계층으로 인식하고, 데이터 계층의 예외를 스프링 예외로 변환해준다.**

**@Configuration : 앞서 보았듯이 스프링 설정 정보로 인식하고, 스프링 빈이 싱글톤을 유지하도록 추가 처리를 한다.**

> **참고 : useDefaultFilters 옵션은 기본으로 켜져 있는데, 이 옵션을 끄면 기본 스캔 대상들이 제외된다.**  
> **그냥 이런 옵션이 있구나 정도 알고 넘어가자.**
