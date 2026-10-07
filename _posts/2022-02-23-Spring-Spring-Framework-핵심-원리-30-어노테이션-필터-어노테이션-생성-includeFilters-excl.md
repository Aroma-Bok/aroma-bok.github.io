---
title: "[Spring] Spring Framework - 핵심 원리 (30) - 어노테이션 필터 + 어노테이션 생성 + includeFilters + excludeFilters + @ComponentScan.Filter + FilterType"
date: 2022-02-23 00:15:22 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["@componentscan.filter", "annotation", "assignable_type", "excludefilters", "filtertype", "includefilters", "어노테이션 생성", "어노테이션 필터"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-30-%EC%96%B4%EB%85%B8%ED%85%8C%EC%9D%B4%EC%85%98-%ED%95%84%ED%84%B0-%EC%96%B4%EB%85%B8%ED%85%8C%EC%9D%B4%EC%85%98-%EC%83%9D%EC%84%B1-includeFilters-excludeFilters-ComponentScanFilter"
---
### **어노테이션 필터 + 어노테이션 생성 + includeFilters + excludeFilters + @ComponentScan. Filter + FilterType**

* * *

**includeFilters : 컴포넌트 스캔 대상을 추가로 지정.**

**excludeFilters : 컴포넌트 스캔에서 제외할 대상을 지정.**

**이번엔 직접 어노테이션을 만들어서 사용해보자. 바로 코드로 알아보자.**

#### **1\. MyIncludeComponent 어노테이션 생성**

```java
@Target(ElementType.TYPE) // TYPE이라 하면, 같은 클래스 레벨이 타겟이 된다.
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyIncludeComponent {
	
}
```

#### **2\. MyExcludeComponent 어노테이션 생성**

```java
@Target(ElementType.TYPE) // TYPE이라 하면, 같은 클래스 레벨이 타겟이 된다.
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyExcludeComponent {
	
}
```

#### **3\. BeanA & BeanB 생성**

**BeanA - @MyIncludeComponent**

**BeanB - @MyExcludeComponent를 각각 지정해준다.**

```java
@MyIncludeComponent
public class BeanA {
}
```

```java
@MyExcludeComponent
public class BeanB {
}
```

#### **4\. ComponentFilterAppConfigTest 테스트 코드 생성**

**getBean("BeanB", BeanB.class) 코드에서 BeanB는 excludeFilters에 포함되어 있기 때문에, 에러가 발생.**

```java
	@Test
	public void filterScan() {
	        ApplicationContext ac = new AnnotationConfigApplicationContext(ComponentFilterAppConfig.class);
	        
	BeanA beanA = ac.getBean("beanA", BeanA.class);
	Assertions.assertThat(beanA).isNotNull();
	
	ac.getBean("BeanB",BeanB.class);
	}
	
	@Configuration @ComponentScan(
	            includeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = MyIncludeComponent.class),
	            excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class)
	)
	static class ComponentFilterAppConfig { 
    }
}
```

**결과를 확인해보면 아래처럼 에러가 발생한다.**

![](/assets/img/posts/98/1.png)

*찾을 수 없는 BeanB !!*

**org.junit.jupiter.api.Assertions.assertThrows를 사용해서 예외처리를 해주자.**

```java
org.junit.jupiter.api.Assertions.assertThrows(NoSuchBeanDefinitionException.class, () -> ac.getBean("beanB", BeanB.class));
```

![](/assets/img/posts/98/2.png)

*테스트 성공!*

#### **정리**

-   **includeFilters에 MyIncludeComponent 어노테이션을 추가해서 BeanA가 스프링 빈에 등록된다.**
-   **excludeFilters에 MyExcludeComponent 어노테이션을 추가해서 BeanB가 스프링 빈에 등록되지 않는다.**

* * *

#### **FilterType 옵션**

1.  **ANNOTATION : 기본값, 어노테이션을 인식해서 동작한다. (기본값이라 생략해도 결과는 똑같이 나온다.)**  
    **ex) org.example.SomAnnotation**
2.  **ASSIGNABLE\_TYPE : 지정한 타입과 자식 타입을 인식해서 동작한다.**  
    **ex) org.example.SomeClass**
3.  **ASPECTJ : AspectJ 패턴 사용**  
    **ex) org.example..\*Service+**
4.  **REGEX : 정규표현식**  
    **ex) org\\. example\\. Default.\***
5.  **CUSTOM : TypeFilter라는 인터페이스를 구현해서 처리**  
    **ex) org.example.MyTypeFilter**

**예를 들어서 BeanA도 빼고 싶으면 다음과 같이 추가하면 된다.**

```java
	@Configuration @ComponentScan(
	            includeFilters = {@ComponentScan.Filter(type = FilterType.ANNOTATION, classes = MyIncludeComponent.class),},
	           // excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class),
	            excludeFilters = { @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class),
	            				 @ComponentScan.Filter(type = FilterType.ASSIGNABLE_TYPE, classes = BeanA.class)}
	)
	static class ComponentFilterAppConfig {
		
	}
```

![](/assets/img/posts/98/3.png)

*beanA도 찾을 수 없다!!*

> **참고 : @Component면 충분하기 때문에, includeFilters를 사용할 일은 거의 없다. excludeFilters는 여러 가지 이유로**  
> **간혹 사용할 때가 있지만 많지는 않다. 특히 최근 스프링 부트는 컴포넌트 스캔을 기본으로 제공하는데,**  
> **개인적으로는 옵션을 변경하면서 사용하기보다는 스프링의 기본 설정에 최대한 맞추어 사용하는 것을 권장하고,**  
> **선호하는 편이다.**
