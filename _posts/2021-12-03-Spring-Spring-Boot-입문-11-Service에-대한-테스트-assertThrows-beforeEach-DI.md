---
title: "[Spring] Spring Boot - 입문 (12) - Service에 대한 테스트 + assertThrows + beforeEach + DI"
date: 2021-12-03 00:30:23 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["assertthrows", "beforeeach", "di", "스프링", "스프링 부트", "예외체크", "의존성 주입"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Boot-%EC%9E%85%EB%AC%B8-11-Service%EC%97%90-%EB%8C%80%ED%95%9C-%ED%85%8C%EC%8A%A4%ED%8A%B8-assertThrows-beforeEach-DI"
---
####  **Service에 대한 테스트 + assertThrows + beforeEach + DI**

* * *

#### **Junit Test 쉽게 만들기**

1.  프로젝트를 오른쪽 마우스 클릭 이후에 **New** > JUnit **Test** Case를 선택
2.  Name 항목에 테스트하고자 하는 클래스명을 입력한 후에...
3.  추가된 클래스를 확인하여 해당 클래스를 통해 테스트를 진행

![](/assets/img/posts/28/1.png)![](/assets/img/posts/28/2.png)

#### **1\. 회원가입 Test** 

**Service에 만들었던 findOne메서드를 호출해서 ID를 받아온다.**

**그리고 assertThat을 사용해서 입력값이 같은지 확인한다.**

**결과는 당연히 True(초록색)**

![](/assets/img/posts/28/3.png)

하지만 중요한 건, 테스트는 예외 플로우가 굉장히 중요하다고 한다.  
**예외도** 잘 발생하는지 **체크**해보자.

#### **2-1. 예외 체크 (중복검사) - try~catch 사용**

try~catch 구문 안에 있는 

Assertions.assertThat(e.getMessage()). isEqualTo("이미 존재하는 회원입니다."); 에서

오류 메시지와 같은 내용을 받아 왔기 때문에 초록색이 뜬다. 

즉, **예외가 잘 발생해서 중복검사를 잘했다는 의미**이다.

![](/assets/img/posts/28/4.png)

#### **2-2. 예외 체크 (중복검사) - assertThrows 사용**

**try~catch보다 좋은 문법이라고 한다.**

**memberService.join(member2)를 호출했을 때, IllegalStateException.class가 발생하기를 기대하고 만든 코드이다.**

**즉, 예외가 발생하면 True(초록색)**

![](/assets/img/posts/28/5.png)

당연히 NullPointerException.class가 발생해야 하는데

Service에는 NullPointerException를 예외로 잡지 않았기 때문에 빨간색(false)이 표시된다.

![](/assets/img/posts/28/6.png)

![](/assets/img/posts/28/7.png)

또는 아래와 같이, 예외를 받아서 메시지가 같은지 검증해도 된다고 한다.

![](/assets/img/posts/28/8.png)

#### **3\. DI (Dependency Injection) - 의존성 주입**

테스트 코드를 작성하면서 약간의 문제가 있었다. 

바로 MemberServiceTest에서 사용하는 memberRepository와

MemberService에서 사용하는 memberRepository와 다르다는 것이다.

![](/assets/img/posts/28/9.png)

![](/assets/img/posts/28/10.png)

다르지만 같이 사용할 수 있게 해주는 이유는, MemoryMemberRepository에서 static으로 선언을 해놨기 때문에 가능하다고 한다. (static으로 선언되어있지 않으면 위에처럼 사용할 수 없다고 함)

![](/assets/img/posts/28/11.png)

그래서 같은 memberRepository를 사용할 수 있도록 MemberService를 아래와 같이 재설정해준다.

![](/assets/img/posts/28/12.png)

![](/assets/img/posts/28/13.png)

이렇게 DI를 사용해서 같은 memberService를 사용할 수 있게 만들 수 있었다.

필자도 아직 DI에 대한 개념이 약하다. 다음 강의를 들으면서 공부해야겠다.
