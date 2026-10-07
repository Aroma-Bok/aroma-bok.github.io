---
title: "[Spring] JSESSIONID와 WAS 세션 갱신의 관계 : 동작 방식 정리"
date: 2025-07-24 13:09:15 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["jsessionid", "jsessionid 요청과 was", "was jsessionid 자동 갱신", "세션"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-JSESSIONID%EC%99%80-WAS-%EC%84%B8%EC%85%98-%EA%B0%B1%EC%8B%A0%EC%9D%98-%EA%B4%80%EA%B3%84-%EB%8F%99%EC%9E%91-%EB%B0%A9%EC%8B%9D-%EC%A0%95%EB%A6%AC-1"
---
**웹 개발에서 로그인이나 사용자 인증 기능을 구현할 때, Session(세션)과 JSESSIONID는 가장 기본적이면서 중요한 개념이다. 최근 실무에서 접하면서 알게 된 JSESSIONID와 세션 갱신의 동작 방식을 정리했다.**

* * *

#### **세션(Session)과 JSESSIONID란?**

-   **세션(Session)**  
    **서버가 사용자의 정보를 일정 시간 동안 저장하는 방법**  
    **주로 로그인정보, 장바구니, 최근 조회 내역등 사용자별 개별 데이터를 저장할 때 사용**
-   **JSESSIONID**  
    **WAS(톰캣, JBoss 등)가 발급하는 세션 식별자 쿠키**  
    **브라우저는 이 값을 쿠키로 저장하고, 서버에 요청할 때마다 같이 전송**

#### **세션 갱신(연장)과 JSESSIONID의 관계**

-   **JSESSIONID가 포함된 요청이 올 때마다 세션이 자동으로 연장된다.**
-   **서버(WAS)는 JSESSIONID 쿠키 값을 보고 사용자를 식별**
-   **클라이언트(브라우저)가 서버에 요청할 때 JSESSIONID를 같이 전송하면,**  
    **서버는 해당 JSESSIONID에 매핑된 세션 정보를 꺼내어 사용**  
    **이때 세션의 "lastAccessedTime"이 현재 시간으로 자동 갱신**
-   **이렇게 lastAccessedTime이 갱신될 때마다, 세션 타임아웃까지 남은 시간이 다시 채워진다.**

**세션(Session)의 타임아웃 시간은 해당 JSESSIONID가 담긴 HTTP 요청이 오면 다시 초기화된다.**  
**즉, 일정 시간마다 사용자가 서버에 접속(요청)만 하면, 세션이 계속 살아있게 된다.**

* * *

![](https://blog.kakaocdn.net/dna/cqsXs3/btsPlRzx16U/AAAAAAAAAAAAAAAAAAAAABOCrLI0gjlbAJMgiqVQVwUfZLB8iQGHGegVlx65JyZ8/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1793458799&allow_ip=&allow_referer=&signature=MBTblaQFZUhy8gxs8l0TLkecNn8%3D)

*Header에 쿠키가 같이 넘어오고있다.*

**개발 중 문제점은 이렇다.**

-   **모든 API에 대해서 세션타임이 갱신되도록 설정**
-   **특정 API에 대해서는 세션타임이 갱신되지 않도록 설정**
-   **하지만, 지정한 특정 API가 호출돼도 세션이 죽지 않고 계속 갱신**

**왜 이렇게 동작할까? 그리고 주의해야 하는 것은?**

-   **JSESSIONID가 담긴 요청은 모두 세션을 연장**  
    **심지어 단순 알림 요청, 상태확인 등에도 쿠키가 같이 가면 세션을 WAS에서 자동 연장**  
    **의도치 않게 세션이 무한정 연장**
-   **알림용, 헬스체크용 API는 가능하면 JSESSIONID 없이 호출**  
    **fetch/axios에서 쿠키 미포함 옵션, 서버단에서 세션 미생성 처리**

* * *

**정리**

-   **JSESSIONID가 포함된 모든 요청마다 세션 타임아웃이 다시 연장**
-   **세션 만료는 마지막 JSESSIONID 요청 -> 지정 타임아웃 경과 시 발생**
-   **실무에서는 알림, 상태체크처럼 "세션 유지와 상관없는 요청"엔 JSESSIONID 없이 보낼 수 있게 처리하는 것이 바람직**
