---
title: "[Spring] Spring Boot - 입문(10) - 회원 리포지토리 테스트 케이스 작성 + Junit, Assertions, assertThat, assertEquals, isEqualTo, @AfterEach"
date: 2021-12-02 21:56:55 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["@aftereach", "assertequals", "assertthat", "asssertions", "isequalto", "tdd", "스프링", "스프링 부트", "테스트기반개발"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Boot-%EC%9E%85%EB%AC%B810-%ED%9A%8C%EC%9B%90-%EB%A6%AC%ED%8F%AC%EC%A7%80%ED%86%A0%EB%A6%AC-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%BC%80%EC%9D%B4%EC%8A%A4-%EC%9E%91%EC%84%B1-Junit-Assertions-assertThat-assertEquals-isEqualTo-AfterEach"
---
**회원 리포지토리 테스트 케이스 작성**

* * *

만들었던 Repository가 정상 작동할까?라는 의문이 들기 마련이다.

이거를 검증하기 위해서 테스트 코드를 작성해서 검증해야 한다.

보통 main메서드 또는 Controller로 해당 기능을 검증하지만 이렇게 하면 반복적으로 하기에는 시간과 노력이 많이 들고 어렵다고 한다.

그래서 Junit이라는 프레임워크로 테스트 코드를 작성해서 테스트 코드를 실행하는 방법이 있다.

![](/assets/img/posts/26/1.png)

*기존에 만들었던 Save 메서드*

#### **1\. Boolean으로 True, False 확인 - Save메서드 검증**

![](/assets/img/posts/26/2.png)

하지만, 글자로 보는 게 불편하다면, **Assertions(org.junit.jupiter.api)**을 사용해서도 테스트 결과가 파악 가능하다.

#### **2\. Assertions(org.junit.jupiter.api) - **Save메서드 검증****

![](/assets/img/posts/26/3.png)

![](/assets/img/posts/26/4.png)

**Assertions.assertEquals(Expected, Actual); => Actual이 Expected와 값이 같은지 검증**

**같으면, 초록색 || 다르면, 빨간색으로 표시**

#### **3\. Assertions(org.assertj.core.api) - 가장 많이 쓴다는 방법 - **Save, findByName 메서드 검증****

![](/assets/img/posts/26/5.png)

![](/assets/img/posts/26/6.png)

**Assertions.assertThat(Expected). isEqualTo(Acutal);** 

**Expected와 **Acutal이 같다면, 초록색 || 다르면, 빨간색이 표시****

**\*실무에서는 Build Tool과 엮어서 테스트 코드가 통과되지 않으면 다음으로 넘어가지 못하게끔 한다고 함.**

※ 2개 메서드를 검증하고 마지막 3번째 메서드를 검증하는 과정에서 에러가 발생하였다.

잘 돌아가던 2번째 메서드에서 에러가 발생했는데... 이유는 **테스트의 순서는 보장이 안된다고 한다.**

findAll (3번째 메서드)가 실행이 되었고 그 안에서 이미 작성해 놓은 내용이 저장되었기 때문에 그렇다고 한다.

![](/assets/img/posts/26/7.png)

*반환타입이 List이기 때문에 size로 맞는지 검증 진행*

**그래서 테스트코드가 끝나면 clear를 해주어야 한다. 방법은 테스트하는 클래스 안에 클리어 메서드를 생성해야 한다.**

![](/assets/img/posts/26/8.png)

*테스트 하려고 하는 메서드안에 clear기능 하는 메서드 생성*

다시 Test 코드로 돌아와 아래와 같이 **'@AfterEach'** 어노테이션을 작성해서 각 **테스트가 끝나면 clear가 될 수 있도록 콜백 메서드 작성**해야 한다. **(중요한 내용!!!)**

![](/assets/img/posts/26/9.png)

그러면 테스트 코드가 다 정상적으로 돌아가게 된다.

![](/assets/img/posts/26/10.png)

**※ 테스트 코드를 작성할 때는 서로 의존관계가 없이 작성되어야 한다. 그러기 위해서는 저장소나 공용 데이터들을 깔끔하게 지워주어야 한다.**

'클래스 -> 테스트'로 보통 개발이 진행이 되지만,

**'테스트 -> 클래스'처럼 순서를 뒤집어서** 진행하는 것이 우리가 흔히 듣던 **'TDD-Test Driven Development : 테스트 기반 개발'라고** 한다. 

**테스트 코드 없이 개발하는 것은 불가능하다고 한다. 물론 개발을 하면 할 수 있지만 정말 많은 문제가 생긴다고 한다.**

**꼭 테스트 코드를 습관화해두는 게 필요할 것 같다!**
