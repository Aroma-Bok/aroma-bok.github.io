---
title: "[Spring] Spring Boot - 입문(11) - 회원 서비스 개발 +  Service 생성 + Extract Method"
date: 2021-12-02 22:49:14 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["extract method", "service", "스프링", "스프링부트", "스프링부트 입문"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Boot-%EC%9E%85%EB%AC%B811-%ED%9A%8C%EC%9B%90-%EC%84%9C%EB%B9%84%EC%8A%A4-%EA%B0%9C%EB%B0%9C-Service-%EC%83%9D%EC%84%B1-Extract-Method"
---
**회원 서비스 클래스를 만들어보자**

* * *

회원 도메인과 리포지터리를 활용해서 실제 비즈니스 로직을 작성하는 패키지 생성

#### **1\. 회원가입 기능을 담당하는 메서드 생성 - Join**

기존에 MemoryMemberRepository에서

![](/assets/img/posts/27/1.png)

**IllegalStateException : Controller에서 @RequestMapping의 값이 중복되어 나타나는 에러!**

**즉, 중복된 이름을 받지 못하게 Service에서 처리!**

**※ Optional 사용할 때의 Tip : Optional을 바로 반환하는 것은 좋지 않다고 한다.**

**그래서 아래처럼 축약해서 사용하는 것을 권장한다고 한다.** 

**(기존에 이미 Optional로 반환 타입을 지정해두었기 때문에,**

**memberRepository.findByName(member.getName())은 이미**

**Optional이기 때문에 사용 가능 하다고 함)**

![](/assets/img/posts/27/2.png)

#### **2\. Extract Method (메서드 추출)** 

**리팩터링 중에서 간단하면서도 강력한 방법인 Extract Method (메서드 추출)**

**'코드의 목적이 잘 드러나도록 메서드의 이름을 지어 별도의 메서드를 뽑아내는 방법'**

![](/assets/img/posts/27/3.png)

로직이 이렇게 길어지면 **Extract Method**를 통해서 메서드를 분리 시켜주는 것이 좋다고 한다.

![](/assets/img/posts/27/4.png)

*이클립스 단출키 : Alt + Shift + M*

메서드의 이름을 지정하면 아래와 같이 메서드가 분리되는 걸 확인할 수 있다.

![](/assets/img/posts/27/5.png)

#### **3\. Service 안에서의 네이밍은 비즈니스 하게 작성하라**

![](/assets/img/posts/27/6.png)

MemberRepository 인터페이스 안에는 뭔가 되게 단순히 저장소에 넣었다 뺏다만 하는 것처럼 네이밍이 쉽다.

![](/assets/img/posts/27/7.png)

하지만, Service는 좀 더 비즈니스에 가까운 네이밍을 사용한다. 보통 Service는 비즈니스 용어를 사용해야 기획자든 개발자든 쉽게 이해하고 커뮤니케이션이 원활할 수 있다고 한다. (그렇다고 한다..)

#### **4\. 전체회원 조회, 한 명의 회원 조회 기능을 담당하는 메서드 생성 - findMembers, findOne**

![](/assets/img/posts/27/8.png)

이렇게 Service를 작성해 보았다. 이것 또한 잘 작동하는지 Test코드를 작성해서 검증해야 한다!
