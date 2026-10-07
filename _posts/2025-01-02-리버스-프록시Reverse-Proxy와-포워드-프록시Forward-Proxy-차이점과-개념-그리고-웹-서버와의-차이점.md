---
title: "리버스 프록시(Reverse Proxy)와 포워드 프록시(Forward Proxy) 차이점과 개념 그리고 웹 서버와의 차이점"
date: 2025-01-02 11:43:21 +0900
categories: ["ETC", "IT Knowledge"]
tags: ["리버스 프록시", "리버스 프록시 개념", "리버스 프록시 웹서버 차이", "리버스 프록시 장점", "리버스 프록시 포워드 프록시", "웹 서버", "포워드 프록시 장점", "프록시", "프록시 역할"]
tistory_url: "https://aroma-bok.tistory.com/entry/%EB%A6%AC%EB%B2%84%EC%8A%A4-%ED%94%84%EB%A1%9D%EC%8B%9CReverse-Proxy%EC%99%80-%ED%8F%AC%EC%9B%8C%EB%93%9C-%ED%94%84%EB%A1%9D%EC%8B%9CForward-Proxy-%EC%B0%A8%EC%9D%B4%EC%A0%90%EA%B3%BC-%EA%B0%9C%EB%85%90-%EA%B7%B8%EB%A6%AC%EA%B3%A0-%EC%9B%B9-%EC%84%9C%EB%B2%84%EC%99%80%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90"
---
### **리버스 프록시(Reverse Proxy)**

-   **클라이언트의 요청을 받아 내부 서버로 전달하고, 서버의 응답을 다시 클라이언트에 전달하는 중개 서버**  
    **주로 서버 보호, 로드 밸런싱, 캐싱과 같은 목적을 위해 사용**

#### **리버스 프록시의 동작 방식**

1.  **클라이언트는 실제 서버를 직접 호출하지 않고, 리버스 프록시를 호출**
2.  **리버스 프록시는 요청을 받아 적절한 내부 서버로 전달**
3.  **내부 서버는 요청을 처리하고 응답을 리버스 프록시로 반환**
4.  **리버스 프록시는 이 응답을 클라이언트에 반환**

#### **리버스 프록시의 주요 역할**

-   **보안 강화**  
    **클라이언트는 실제 서버(IP, 포트)를 알 수 없다. 서버를 숨길 수 있어 공격에 대한 방어력을 높인다.**  
    **SSL/TLS 암호화를 중앙에서 처리(SSL 오프로드)하여 서버의 부담을 줄이고 보안을 강화**
-   **로드 밸런싱**  
    **여러 대의 서버에 요청을 분배하여 서버를 부하를 균등하게 유지**  
    **고가용성을 제공하며 서버가 다운되었을 때 다른 서버로 요청을 전달**
-   **캐싱**  
    **반복적인 요청에 대해 응답 데이터를 캐싱하여 서버의 부하를 줄일고 클라이언트에게 더 빠른 응답을 제공**
-   **압축 및 성능 최적화**  
    **데이터 압축을 수행하거나, 클라이언트와 서버 간의 연결을 최적화하여 응답 속도를 높인다**
-   **서버 통합**  
    **서로 다른 서비스나 애플리케이션 서버를 단일 URL 아래 통합해, 클라이언트는 일관된 인터페이스를 사용할 수 있다.**

* * *

#### **리버스 프록시 vs 포워드 프록시**

<table style="border-collapse: collapse; width: 100%; height: 120px;" border="1" data-ke-align="alignLeft"><tbody><tr><td><span style="font-family: 'Noto Serif KR';"><b>특징</b></span></td><td><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>리버스 프록시</b></span></td><td><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>포워드 프록시</b></span></td></tr><tr><td><span style="font-family: 'Noto Serif KR';"><b>주요 사용자</b></span></td><td><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>클라이언트 요청을 처리하는 서버</b></span><br><span style="font-family: 'Noto Serif KR';"><b>(서비스 제공자)</b></span></td><td><span style="font-family: 'Noto Serif KR';"><b><span style="color: #ee2323;">클라이언트가 인터넷에 접근할 때 사용</span> (사용자)</b></span></td></tr><tr><td><span style="font-family: 'Noto Serif KR';"><b>작동 위치</b></span></td><td><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>클라이언트와 서버 사이, 서버 앞에서 동작</b></span></td><td><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>클라이언트와 인터넷 사이, 클라이언트 앞에서 동작</b></span></td></tr><tr><td><span style="font-family: 'Noto Serif KR';"><b>목적</b></span></td><td><span style="font-family: 'Noto Serif KR';"><b>서버 보호, 로드 밸런싱, 캐싱, 보안 강화</b></span></td><td><span style="font-family: 'Noto Serif KR';"><b>사용자 익명성 보장, IP 차단 우회</b></span></td></tr><tr><td><span style="font-family: 'Noto Serif KR';"><b>예시</b></span></td><td><span style="font-family: 'Noto Serif KR';"><b>Nginx, Apache HTTP Server, HAProxy</b></span></td><td><span style="font-family: 'Noto Serif KR';"><b>Squid Proxy, VPN, Web Proxy</b></span></td></tr></tbody></table>

* * *

### **리버스 프록시는 그러면 웹 서버 인가?**

**"리버스 프록시" 자체는 역할을 의미하며, 반드시 "웹 서버"라는 하드웨어나 소프트웨어를 의미하지는 않는다.**  
**그러나, 리버스 프록시 기능은 종종 "웹 서버 소프트웨어"나 전용 "리버스 프록시 소프트웨어"를 통해 구현된다.**

* * *

#### **리버스 프록시 vs 웹 서버**

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft"><tbody><tr><td><span style="font-family: 'Noto Serif KR';"><b>특징</b></span></td><td><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>리버스 프록시</b></span></td><td><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>웹 서버</b></span></td></tr><tr><td><span style="font-family: 'Noto Serif KR';"><b>주요 역할</b></span></td><td><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>클라이언트 요청을 받아 내부 서버에 <u>전달</u>하고 응답을 반환</b></span></td><td><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>클라이언트 요청을 <u>직접 처리</u>하며 정적/동적 컨텐츠를 제공</b></span></td></tr><tr><td><span style="font-family: 'Noto Serif KR';"><b>목적</b></span></td><td><span style="font-family: 'Noto Serif KR';"><b>보안, 로드 밸런싱, 캐싱, SSL 오프로드 등</b></span></td><td><span style="font-family: 'Noto Serif KR';"><b>HTML, CSS, JavaScript 등의 정적 파일 제공 및 동적</b></span><br><span style="font-family: 'Noto Serif KR';"><b>요청 처리</b></span></td></tr><tr><td><span style="font-family: 'Noto Serif KR';"><b>실제 컨텐츠 관리</b></span></td><td><span style="font-family: 'Noto Serif KR';"><b>요청을 전달하고, 내부 서버가 컨텐츠를 처리</b></span></td><td><span style="font-family: 'Noto Serif KR';"><b>클라이언트가 요청한 컨텐츠를 직접 생성 또는 제공</b></span></td></tr><tr><td><span style="font-family: 'Noto Serif KR';"><b>예시</b></span></td><td><span style="font-family: 'Noto Serif KR';"><b>Nginx, HAProxy, Apache(프록시 설정 포함)</b></span></td><td><span style="font-family: 'Noto Serif KR';"><b>Nginx, Apache, IIS, Tomcat, LiteSpeed</b></span></td></tr></tbody></table>

**리버스 프록시는 반드시 웹 서버 자체를 의미하는 것은 아니지만, 많은 웹 서버 소프트웨어가 리버스 프록시  
기능을 포함하고 있어, 웹 서버를 리버스 프록시로 사용하는 경우가 흔하다.**

**즉, 리버스 프록시는 역할이고, 이를 수행하기 위해 웹 서버 (Nginx, Apach)나 전용 소프트웨어  
(HAProxy, Traefic)가 사용될 수 있다.**
