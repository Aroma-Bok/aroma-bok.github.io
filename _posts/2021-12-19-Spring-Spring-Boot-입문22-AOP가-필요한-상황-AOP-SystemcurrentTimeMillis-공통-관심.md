---
title: "[Spring] Spring Boot - 입문(22) - AOP가 필요한 상황 + AOP + System.currentTimeMillis() + 공통 관심 사항 + 핵심 관심 사항"
date: 2021-12-19 16:39:29 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["aop", "aspect oriented programming", "공통 관심 사항", "관점 지향 프로그래밍", "핵심 관심 사항"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Boot-%EC%9E%85%EB%AC%B822-AOP%EA%B0%80-%ED%95%84%EC%9A%94%ED%95%9C-%EC%83%81%ED%99%A9-AOP-SystemcurrentTimeMillis-%EA%B3%B5%ED%86%B5-%EA%B4%80%EC%8B%AC-%EC%82%AC%ED%95%AD-%ED%95%B5%EC%8B%AC-%EA%B4%80%EC%8B%AC-%EC%82%AC%ED%95%AD"
---
#### **AOP가 필요한 상황 + AOP (Aspect Oriented Programming)+ System.currentTimeMillis()**

* * *

#### **AOP (Aspect Oriented Programming) - 관점 지향 프로그래밍**

#### **: 어떤 로직을 기준으로 핵심정인 관점, 부가적인 관점으로 나누어서 보고,**

####   **그 관점을 '기준'으로 각각 모듈화 하겠다는 것.**

**이론적으로만 공부하면 '멘붕'에 빠진다고하는 내용이라고 한다.** 

**'언제' '왜' 사용하는지만 알고 공부하는게 중요하다고 한다.**

**AOP가 필요한 상황**

**1\. 모든 메소드의 호출 시간을 측정하고 싶다면?**

**2\. 공통 관심 사항(Cross-cutting concern) vs 핵심 관심 사항(Core concern)**

**3\. 회원 가입 시간, 회원 조회 시간을 측정하고 싶다면?**

#### **1\. Service에 시간 구하는 소스코드 추가 - 회원가입**

```html
	/*
	 * 회원가입
	 */
	public Long join(Member member) {
	
		long start = System.currentTimeMillis()
		
		try {
			validateDuplicateMember(member);
			memberRepository.save(member); 
			return member.getId(); 
		} finally {
			long finish = System.currentTimeMillis();
			long timeMs = finish - start;
			System.out.println("join = " + timeMs + "ms");
	}
```

#### **원리**

**1\. 'System.currentTimeMillis()'을 이용해서 처음 메소드 시작시간 구하기**

**2\. try~finally 구문 사용**

**3\. 마지막에 무조간 발생하도록 'finally'구문에 시간 구하는 소스코드 작성**

#### **2\. 테스트에서 확인** 

![](/assets/img/posts/41/1.png)

**콘솔에서 확인하면 '회원가입'기능은 '14ms'가 소요된다.** 

![](/assets/img/posts/41/2.png)

**'회원목록'은 '261ms'가 소요된다.**

* * *

**여기서 '문제'는 이렇다.**

**1\. '회원가입','회원 조회'에 시간을 측정하는 기능은 '핵심 관심 사항'이 아니다.**

    **'핵심 관심 사항'은 각 메소드에서 핵심적인 기능' 이다.**

**2\. 시간을 측정하는 로직은 '공통 관심 사항'이다.**

**3\. 시간을 측정하는 로직과 핵심 비즈니스 로직이 섞여서 유지보수가 어렵다.**

**4\. 시간을 측정하는 로직을 별도의 공통 로직으로 만들기 매우 어렵다.**

**5\. 시간을 측정하는 로직을 변경할 때 모든 로직을 찾아가면서 변경해야 한다.**  
**(if, 100개면 100개의 메소드를 수정해야한다.)**
