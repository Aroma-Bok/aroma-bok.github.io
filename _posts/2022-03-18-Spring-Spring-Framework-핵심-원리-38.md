---
title: "[Spring] Spring Framework - 핵심 원리 (38) -어노테이션(@) 만드는 방법 + 스프링이 어노테이션을 읽어오는 원리"
date: 2022-03-18 22:59:49 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["@qualifier", "@어노테이션 만드는 방법", "스프링 프레임워크", "어노테이션 원리", "어노테이션 읽어오는 원리", "프레임워크"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-38"
---
### **어노테이션(@) 만드는 방법 + 스프링이 어노테이션을 읽어오는 원리**

* * *

#### **어노테이션 직접 만들기**

**@Qualifier("mainDiscountPolicy") 이렇게 문자를 적으면 컴파일 시 타입 체크가 안된다.**

**예를 들어, "mainnDiscountPolicy"으로 써도 동작 은하지만, 오류가 발생한다.**

**그래서 다음과 같은 어노테이션을 만들어서 문제를 해결할 수 있다.**

#### **1\. 필요한 어노테이션들을 얻어오자.**

**Qualifier의 어노테이션들을 그대로 따와서 적용해주자.**

```java
@Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER, ElementType.TYPE, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface Qualifier {

	String value() default "";
}
```

#### **2\. 인터페이스 생성 (사용하고자 하는 어노테이션과 이름을 동일하게 해 주는 게 좋다.)**

> **@Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER, ElementType.TYPE,   
> ElementType.ANNOTATION\_TYPE})**  
> **@Retention(RetentionPolicy.RUNTIME)**  
> **@Inherited**  
> **@Documented**  
> **@Qualifier("mainDiscountPolicy") -> 사용하고자 하는 어노테이션 명 부여**

```java
@Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER, ElementType.TYPE, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
@Qualifier("mainDiscountPolicy")
public @interface MainDiscountPolicy {

}
```

#### **3\. 생성한 어노테이션 적용**

**기존 코드를 보면 아래처럼 @Primary 혹은 @Qualifier를 사용하였다.**

```java
@Component
@Primary or @Qualifier("mainDiscountPolicy")
public class RateDiscountPolicy implements DiscountPolicy
```

**하지만 직접 어노테이션을 부여함으로써 더 정확하게 사용할 수 있게 되었다.**

```java
@Component
@MainDiscountPolicy
public class RateDiscountPolicy implements DiscountPolicy
```

**그리고 이걸 사용하는 생성자에도 똑같이 적용을 해주어야 한다.  
@MainDiscountPolicy가 타입명 앞에 붙는다.**

```java
     @Autowired 
     public OrderServiceImpl(MemberRepository memberRepository, @MainDiscountPolicy DiscountPolicy discountPolicy) {
		this.memberRepository =  memberRepository; 
		this.discountPolicy = discountPolicy; 
 }
```

**테스트를 돌려서 확인해보면 결과는 성공한다. 그리고 사용되는 Bean도 @MainDisocuntPolicy를 부여한 rateDiscountPolicy인 것을 확인할 수 있다.**

![](/assets/img/posts/113/1.png)

![](/assets/img/posts/113/2.png)

**물론 @Primary를 잘 사용해서 사용해도 괜찮다. 다양한 상황에서 적용해보자.**

* * *

**정리**

**어노테이션에는 상속이라는 개념이 없다. 이렇게 여러 어노테이션을 모아서 사용하는 기능은 스프링이 지원해주는 기능이다. 자바의 기능이 아니다. @Qualifier 뿐만 아니라, 다른 어노테이션들도 함께 조합해서 사용할 수 있다. 단적으로 @Autowired도 재정의 할 수 있다. 물론 스프링이 제공하는 기능을 뚜렷한 목적 없이 무분별하게 재정의 하는 것은 유지보수에 더 혼란만 가중할 수 있다.**
