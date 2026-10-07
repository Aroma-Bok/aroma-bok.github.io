---
title: "[Spring] Spring Framework - 핵심 원리 (23) - 싱글톤 컨테이너, 웹 애플리케이션과 싱글톤 + 싱글톤 컨테이너 (스프링 컨테이너) + same(==)과 equal 차이 + 싱글톤 패턴 문제점"
date: 2022-02-07 23:57:43 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["== vs equal", "same vs equal", "객체 인스턴스", "스프링 컨테이너", "싱글톤", "싱글톤 컨테이너", "싱글톤 패턴", "싱글톤 패턴 문제점"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Framework-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-23-%EC%8B%B1%EA%B8%80%ED%86%A4-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EC%9B%B9-%EC%95%A0%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98%EA%B3%BC-%EC%8B%B1%EA%B8%80%ED%86%A4-%EC%8B%B1%EA%B8%80%ED%86%A4-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EC%8A%A4%ED%94%84%EB%A7%81-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-same%EA%B3%BC-equal-%EC%B0%A8%EC%9D%B4-%EC%8B%B1%EA%B8%80%ED%86%A4-%ED%8C%A8%ED%84%B4-%EB%AC%B8%EC%A0%9C%EC%A0%90"
---
### **싱글톤 컨테이너, 웹 애플리케이션과 싱글톤 + 싱글톤 컨테이너 (스프링 컨테이너) + same(==)과 equal 차이 + 싱글톤 패턴 문제점**

* * *

**싱글톤은 객체가 JVM안에 딱 1개만 있어야 하는 상태를 의미한다.**

**그렇다면 싱글톤은 왜 많이 사용되는지 알아보자.**

#### **웹 애플리케이션과 싱글톤**

-   **스프링은 태생이 기업용 온라인 서비스 기술을 지원하기 위해 탄생했다.**
-   **대부분의 스프링 애플리케이션은 웹 애플리케이션이다.  
    물론 웹이 아닌 애플리케이션 개발도 얼마든지 개발할 수 있다. E.g) 데몬, 배치 등등**
-   **웹 애플리케이션은 보통 여러 고객이 동시에 요청을 한다.**

![](/assets/img/posts/86/1.png)

**예를 들어서, 각각의 클라이언트가 memberService를 각각 요청한다고 했을 때,  
DI컨테이너에서는 3개의 memberService를 각각 만들어야 한다. 그렇다면 어떤 문제점이 있을까?**

**계속 요청이 올 때마다 계속 객체를 생성해야 한다. 이게 문제다.**

**코드로 확인해보자. 먼저 Test트 폴더에 아래와 같이 작성해서 실행해보자.**

```java
public class SingletonTest {
	@Test
	@DisplayName("스프링이 없는 순수한 DI 컨테이너")
	public void pureContainer() {
		AppConfig appConfig = new AppConfig();
		
		//1. 조회 : 호출할 때 마다 객체를 생성
		MemberService memberService1 = appConfig.memberService();
		
		//1. 조회 : 호출할 때 마다 객체를 생성
		MemberService memberService2 = appConfig.memberService();
		
		//참조값이 다른 것을 확인
		System.out.println("memberService1 = " + memberService1);
		System.out.println("memberService2 = " + memberService2);
	}
}
```

**결과를 확인하면 appConfig가 memberService를 계속 다른 객체를 생성하고 있다.** 

**이렇게 되면, JVM 메모리에 계속 객체가 생성된다. 즉, 메모리를 계속 사용한다는 뜻이다.**

```java
memberService1 = hello.core.member.MemberServiceImpl@a38c7fe
memberService2 = hello.core.member.MemberServiceImpl@6fdbe764
```

**웹 애플리케이션의 특징은 고객의 요청이 참 많은 애플리케이션이다.**

**만약 초당 5만 명의 고객이 요청을 한다면? 초당 5만 개의 객체가 생성이 된다.**

**이렇게 객체를 생성하면 효율적이지도 못하고, 말이 안 되는 구조이다.**

**참고로 Assertions.assertThat을 사용해서 확인해봐도 2개의 객체는 다르다고 확인된다.**

```java
// memberService1 != memberService2
Assertions.assertThat(memberService1).isNotSameAs(memberService2);
```

![](/assets/img/posts/86/2.png)

**호출할 때마다 객체를 생성하고 있다. 그리고 사실 memberService만 생성하는 것이 아니라,**

**memberService를 생성하기 위해서 사용하고 있는 memberRepository 객체도 생성하고 있는 것이다.**

**총 4개의 객체를 만들고 있는 것이다.**

**수많은 요청이 온다고 생각한다면, 메모리 사용량이 정말 어마어마할 것이다.**

* * *

#### **정리**

-   **우리가 만들었던 스프링 없는 순수한 DI 컨테이너인 AppConfig는 요청을 할 때마다 객체를 새로 생성한다.**
-   **고객 트래픽이 초당 100이 나오면 초당 100개 객체가 생성되고 소멸된다! ▶ 메모리 낭비가 심하다.**
-   **해결방안은 해당 객체가 딱 1개만 생성되고, 공유하도록 설계하면 된다. ▶ 싱글톤 패턴**

* * *

#### **싱글톤 패턴**

-   **클래스의 인스턴스가 딱 1개만 생성되는 것을 보장하는 디자인 패턴이다.**
-   **그래서 객체 인스턴스를 2개 이상 생성하지 못하도록 막아야 한다.**
-   **private 생성자를 사용해서 외부에서 임의로 new 키워드를 사용하지 못하도록 막아야 한다.**

**코드로 알아보자.**

```java
package hello.core.singleton;

public class SingletonService {

	//1. static 영역에 객체를 딱 1개만 생성해둔다.
	private static final SingletonService instance = new SingletonService();
	//자기 자신을 static으로 1개 갖고 있는다. 
	
	// 참고 : static으로 만들면, Class 레벨로 올라가기 때문에 딱 1개만 존재하게 된다.
	// 이렇게하면, JVM이 실행되면 static 영역에 있는 자기 자신을 instance에 참조로 넣어놓는다.
	
	
	//2. public으로 열어서 객체 인스턴스가 필요하면 이 static 메서드를 통해서만 조회하도록 허용한다.
	// 즉, SingletonService의 instance를 사용하려면 getInstance만 가능하다.
	public static SingletonService getInstance() {
		return instance;
	}
    	public void logic() {
		System.out.println("싱글톤 객체 로직 호출");
	}
}
```

**하지만, 아무리 위에서 객체를 잘 숨겨놓아도 만약 아래처럼 누군가 new 생성자를 사용해서 만들어버리면 아무 의미가 없다.**

```java
public static void main(String args[]) {
	SingletonService singletonService = new SingletonService();
}
```

**그래서 SingletonService 코드에 private 생성자를 사용해서 외부에서 사용할 수 없도록 막아두는**

**코드를 추가하자.**

```java
private SingletonService() {
}
```

**이렇게 막아두면, 결과적으로는 다른 클래스에서 객체를 new 생성자를 통해서 객체를 생성할 수 없다.**

![](/assets/img/posts/86/3.png)

*private으로 막아서 생성이 불가능*

* * *

#### **정리**

1.  **static 영역에 객체 instance를 미리 하나 생성해서 올려둔다.**
2.  **이 객체 인스턴스가 필요하면 오직 getInstance() 메서드를 통해서만 조회할 수 있다. 이 메서드를 호출하면 항상 같은 인스턴스를 반환한다.**
3.  **딱 1개의 객체 인스턴스만 존재해야 하므로, 생성자를 private으로 막아서 혹시라도 외부에서  
    ****new 키워드로 객체 인스턴스가 생성되는 것을 막는다.**

> **참고 : 제일 좋은 객체 설계는 컴파일 오류만으로 웬만한 오류를 잡히도록 설계하는 것이다.**

**자 이제 그러면, 싱글톤을 사용해서 객체를 사용해보자.**

```java
	@Test
	@DisplayName("싱글톤 패턴을 적용한 객체 사용")
	public void singletonServiceTest() {
		
		// private으로 생성자를 막아두었다. 컴파일 오류가 발생한다.
		//new SingletonService();
		
		SingletonService singletonService1 = SingletonService.getInstance();
		SingletonService singletonService2 = SingletonService.getInstance();
		
		System.out.println("singletonService1 = " + singletonService1);
		System.out.println("singletonService2 = " + singletonService2);
	}
}
```

**결과를 확인하면 똑같은 객체 인스턴스가 생성되는 것을 확인할 수 있다.**

```java
singletonService1 = hello.core.singleton.SingletonService@5f8e8a9d
singletonService2 = hello.core.singleton.SingletonService@5f8e8a9d
```

**추가적으로 Assertions.assertThat을 사용해서 검증해봐도 2개의 객체가 같은 것을 확인할 수 있다.**

```java
Assertions.assertThat(singletonService1).isSameAs(singletonService2);
```

![](/assets/img/posts/86/4.png)

> **참고**  
> **same(==) vs equal 차이**  
> **1\. same(==)의 경우=> 동일한 객체를 참조하는지, 다른 객체를 참조하는지 알아볼 때 사용**  
> **2\. eual의 경우       => 비교대상의 주소 값이 아닌, 데이터 값을 비교한다.**

#### **정리**

1.  **private으로 new 키워드를 막아두었다.**
2.  **호출할 때마다 같은 객체 인스턴스를 반환하는 것을 확인할 수 있다.**

> **참고 : 싱글톤 패턴을 구현하는 방법은 여러 가지가 있다. 여기서는 객체를 미리 생성해두는 가장 단순하고 안전한 방법을 선택했다.**

#### **싱글톤 패턴 문제점**

1.  **싱글톤 패턴을 구현하는 코드 자체가 많이 들어간다.**
2.  **의존관계상 클라이언트가 구체 클래스에 의존한다. ▶ DIP를 위반한다.**
3.  public static SingletonService getInstance() { return instance;}
4.  **클라이언트가 구체 클래스에 의존해서 OCP 원칙을 위반할 가능성이 높다.**
5.  **테스트하기 어렵다.**
6.  **내부 속성을 변경하거나 초기화하기 어렵다.**
7.  **private 생성자로 자식 클래스를 만들기 어렵다.**
8.  **결론적으로 유연성이 많이 떨어진다.**
9.  **안티 패턴으로 불리기도 한다.**

**이렇게 단점이 많다면, 과연 싱글톤 패턴을 사용하라는 것일까? 말라는 것일까?**

**이러한 문제의 해결책은 싱글톤 컨테이너(스프링 컨테이너)가 해결해준다.**

-   **스프링 컨테이너는 싱글톤 패턴의 문제점을 해결하면서, 객체 인스턴스를 싱글톤으로 관리한다.**
-   **지금까지 우리가 학습한 스프링 빈이 바로 싱글톤으로 관리되는 빈이다.**

**다음에는 싱글톤 컨테이너(스프링 컨테이너)를 정리해보자!**
