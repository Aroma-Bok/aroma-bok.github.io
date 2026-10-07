---
title: "[Spring] Spring Framework - 핵심 원리 (22) - 스프링 빈 설정 메타 정보 (BeanDefinition)"
date: 2022-02-05 00:42:56 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["bean 등록하는 방법", "beanclassname", "beandifinition", "factorybeanname", "factorymethodname", "scope", "스프링 빈 메타 정보", "팩토리 메서드"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-22-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B9%88-%EC%84%A4%EC%A0%95-%EB%A9%94%ED%83%80-%EC%A0%95%EB%B3%B4-BeanDefinition"
---
### **스프링 빈 설정 메타 정보 (BeanDefinition) + 빈 등록 방법 2가지 + **AnnotationConfigApplicationContext + **GenericXmlApplicationContext******

* * *

**스프링 빈 설정 메타 정보 - BeanDefinition**

-   **스프링은 어떻게 다양한 설정 형식을 지원하는 것일까? 그 중심에는 BeanDefinition이라는 추상화가 있다.**

**쉽게 이야기해서 역할과 구현을 개념적으로 나눈 것이다!**

-   **XML을 읽어서 BeanDefinition을 만들면 된다.**
-   **자바 코드를 읽어서 BeanDefinition을 만들면 된다.**
-   **스프링 컨테이너는 자바 코드인지, XML인지 몰라도 된다. 오직 BeanDefinition만 알면 된다.**

**BeanDefinition을 빈 설정 메타 정보라 한다.**

-   **@Bean, <bean>에 각각 하나씩 메타 정보가 생성된다.**

**스프링 컨테이너는 이 메타정보를 기반으로 스프링 빈을 생성한다.**

![](/assets/img/posts/85/1.png)

**스프링 컨테이너가 BeanDefinition에만 의존한다. 즉, 추상화에만 의존하도록 만든 설계이다.** 

**참고로 BeanDefinition은 Inteface이다.**

```java
public interface BeanDefinition extends AttributeAccessor, BeanMetadataElement
```

**이 내용을 코드레 벨로 조금 더 깊게 들어가면 아래와 같다.**

![](/assets/img/posts/85/2.png)

-   **AnnotationConfigApplicationContext는 AnnotatedBeanDefinitionReader를 사용해서 AppConfig.class를 읽고 BeanDifinition을 생성한다.**  
    
    ```java
    public class AnnotationConfigApplicationContext extends GenericApplicationContext implements AnnotationConfigRegistry {
    	private final AnnotatedBeanDefinitionReader reader;
    	private final ClassPathBeanDefinitionScanner scanner;​
    ```
    
-   **GenericXmlApplicationContext는 XmlBeanDefinitionReader를 사용해서 appConfig.xml 설정 정보를 읽고 BeanDefinition을 생성한다.**  
    
    ```java
    public class GenericXmlApplicationContext extends GenericApplicationContext {
    	private final XmlBeanDefinitionReader reader = new XmlBeanDefinitionReader(this);​
    ```
    
-   **새로운 형식의 설정 정보가 추가되면, XxxBeanDefinitionReader를 만들어서 BeanDefinition을 생성하면 된다.**

* * *

**코드로 BeanDefinition을 정리해보자.**

```java
import org.junit.Test;
import org.junit.jupiter.api.DisplayName;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import hello.core.AppConfig;

public class BeanDefinitionTest {
	
	AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

	@Test
	@DisplayName("빈 설정 메타정보 확인")
	public void findApplicationBean() {
		String[] beanDefinitionNames = ac.getBeanDefinitionNames(); //이름들을 꺼낸다.
		
		for(String beanDefinitionName: beanDefinitionNames ) {
			BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);
			
			if(beanDefinition.getRole() == beanDefinition.ROLE_APPLICATION) {
				System.out.println("beanDefinitionName = " + beanDefinitionName + 
						"beanDefinition =" + beanDefinition);
			}
		}
	}
}
```

```java
beanDefinitionName = appConfigbeanDefinition =Generic bean: class [hello.core.AppConfig$$EnhancerBySpringCGLIB$$2c4c7f60]; scope=singleton; abstract=false; lazyInit=null; autowireMode=0; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=null; factoryMethodName=null; initMethodName=null; destroyMethodName=null
beanDefinitionName = memberServicebeanDefinition =Root bean: class [null]; scope=; abstract=false; lazyInit=null; autowireMode=3; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=appConfig; factoryMethodName=memberService; initMethodName=null; destroyMethodName=(inferred); defined in hello.core.AppConfig
beanDefinitionName = memberRepositorybeanDefinition =Root bean: class [null]; scope=; abstract=false; lazyInit=null; autowireMode=3; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=appConfig; factoryMethodName=memberRepository; initMethodName=null; destroyMethodName=(inferred); defined in hello.core.AppConfig
beanDefinitionName = orderServicebeanDefinition =Root bean: class [null]; scope=; abstract=false; lazyInit=null; autowireMode=3; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=appConfig; factoryMethodName=orderService; initMethodName=null; destroyMethodName=(inferred); defined in hello.core.AppConfig
beanDefinitionName = discountPolicybeanDefinition =Root bean: class [null]; scope=; abstract=false; lazyInit=null; autowireMode=3; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=appConfig; factoryMethodName=discountPolicy; initMethodName=null; destroyMethodName=(inferred); defined in hello.core.AppConfig
```

**※ ApplicationContext 대신에 AnnotationConfigApplicationContext을 사용하는 이유**  
**\=> getBeanDefinition 기능이 없다.**

#### **BeanDifinition 살펴보기**

**BeanDefinition 정보**

-   **BeanClassName : 생성할 빈의 클래스 명(자바 설정처럼, 팩토리 역할의 빈을 사용하면 없음)**
-   **FactoryBeanName : 팩토리 역할의 빈을 사용할 경우 이름, E.g ) appConfig**
-   **factoryMethodName : 빈을 생성할 팩토리 메서드 지정 E.g) memberService**
-   **Scope : 싱글톤(기본값)**
-   **lazyInit : 스프링 컨테이너를 생성할 때 빈을 생성하는 것이 아니라, 실제 빈을 사용할 때까지 최대한 생성을 지연 처리하는지 여부**
-   **InitMethodName : 빈을 생성하고, 의존관계를 적용한 뒤에 호출되는 초기화 메서드 명**
-   **DestoryMethodName : 빈의 생명주기가 끝나서 제거하기 직전에 호출되는 메서드 명**
-   **Constructor arguments, Properties : 의존관계 주입에서 사용한다. (자바 설정처럼, 팩토리 역할의 빈을 사용하면 없음)**

* * *

#### **정리**

-   **BeanDefinition을 직접 생성해서 스프링 컨테이너에 등록할 수 도 있다. 하지만 실무에서 BeanDefinition을**  
    **직접 정의하거나 사용할 일은 거의 없다.**
-   **BeanDefinition에 대해서는 너무 깊이 있게 이해하기보다는, 스프링이 다양한 형태의 설정 정보를**  
    **BeanDefinition으로 추상화해서 사용하는 것 정도만 이해하면 된다.**
-   **가끔 스프링 코드나 스프링 관련 오픈 소스의 코드를 볼 때, BeanDefinition이라는 것이 보일 때가 있다.  
    이때 이러한 메커니즘을 떠올리면 된다.**

**그리고 GenericXmlApplicationContext을 사용해서 테스트를 돌려보면 아래와 같다.**

**결과의 차이점을 보면, xml은 bean에 대한 정보가 명확하게 등록이 되어있다.**

**AnnotationConfigApplicationContext는 class가 null이고, factoryBeanName , factoryMethodName이 표시**

**GenericXmlApplicationContext는 factoryBeanName, factoryMethodName이 null이고, class가 표시**

```java
public class BeanDefinitionTest {
	
	GenericXmlApplicationContext ac = new GenericXmlApplicationContext("appConfig.xml");
	
	@Test
	@DisplayName("빈 설정 메타정보 확인")
	public void findApplicationBean() {
		String[] beanDefinitionNames = ac.getBeanDefinitionNames(); //이름들을 꺼낸다.
		
		for(String beanDefinitionName: beanDefinitionNames ) {
			BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);
			
			if(beanDefinition.getRole() == beanDefinition.ROLE_APPLICATION) {
				System.out.println("beanDefinitionName = " + beanDefinitionName + 
						"beanDefinition =" + beanDefinition);
			}
		}
	}
}
```

```java
beanDefinitionName = memberServicebeanDefinition =Generic bean: class [hello.core.member.MemberServiceImpl]; scope=; abstract=false; lazyInit=false; autowireMode=0; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=null; factoryMethodName=null; initMethodName=null; destroyMethodName=null; defined in class path resource [appConfig.xml]
beanDefinitionName = memberRepositorybeanDefinition =Generic bean: class [hello.core.member.MemoryMemberRepository]; scope=; abstract=false; lazyInit=false; autowireMode=0; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=null; factoryMethodName=null; initMethodName=null; destroyMethodName=null; defined in class path resource [appConfig.xml]
beanDefinitionName = orderServicebeanDefinition =Generic bean: class [hello.core.order.OrderServiceImpl]; scope=; abstract=false; lazyInit=false; autowireMode=0; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=null; factoryMethodName=null; initMethodName=null; destroyMethodName=null; defined in class path resource [appConfig.xml]
beanDefinitionName = discountPolicybeanDefinition =Generic bean: class [hello.core.discount.RateDiscountPolicy]; scope=; abstract=false; lazyInit=false; autowireMode=0; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=null; factoryMethodName=null; initMethodName=null; destroyMethodName=null; defined in class path resource [appConfig.xml]
```

#### **Bean을 등록하는 방법 대표 2가지**

**1\. 직접 스프링 빈 등록 : E.g) xml에 직접 등록하는 방법**

**2\. 팩토리 메서드 사용 : Java코드를 사용해서 등록하는 방법 E.g) AppConfig.class**
