---
title: "[Web] HTTP와 HTTPS 개념 정리 그리고 SSL/TLS"
date: 2025-01-03 11:08:00 +0900
categories: ["Solution & Tools", "Web"]
tags: ["http handshake", "http http 개념", "http https 차이점", "http 세션키", "https 장단점", "ssl/tls", "ssl/tls handshake", "공개키 비공개키", "세션키 생성"]
tistory_url: "https://aroma-bok.tistory.com/entry/Web-HTTP%EC%99%80-HTTPS-%EA%B0%9C%EB%85%90-%EC%A0%95%EB%A6%AC-%EA%B7%B8%EB%A6%AC%EA%B3%A0-SSLTLS"
---
![](/assets/img/posts/298/1.png)

### **  
HTTP (Hyper Text Transfer Protocol)**

-   **인터넛에서 클라이언트(웹 브라우저)와 서버(웹 서버) 간에 데이터를 주고받기 위한 프로토콜**
-   **주로 웹 페이지의 텍스트, 이미지, 비디오 등의 리소스를 전달**
-   **비암호화 방식으로 데이터가 전송되기 때문에 보안에 취약**
-   **URL의 시작이 http:// 로 표시**

#### **특징**

-   **빠른 데이터 전송**
-   **보안성이 낮음 : 중간에서 데이터가 도청(스니핑) 또는 변조될 수 있음**

* * *

### **HTTPS (Hyper Text Transfer Protocol Secure)**

-   **HTTP + SSL/TLS를 사용해 데이터를 암호화하는 프로토콜**
-   **HTTP보다 보안성이 높으며 민감한 정보(로그인, 결제 정보 등) 전송에 적합**
-   **URL의 시작이 https:// 로 표시**

#### **특징**

-   **암호화 : 데이터를 암호화해 전송, 도청 및 변조방지**
-   **서버 인증 : 인증서를 사용해 클라리언트가 신뢰할 수 있는 서버임을 보장**
-   **데이터 무결성 : 데이터가 전송 중 변경되지 않았음을 보장**

* * *

#### **HTTP와 HTTPS의 주요 차이**

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft"><tbody><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>항목</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b><span style="color: #ee2323;">HTTP</span></b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b><span style="color: #ee2323;">HTTPS</span></b></span></td></tr><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>보안성</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b><span style="color: #ee2323;">비암호화(평문 전송, 취약)</span></b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b><span style="color: #ee2323;">암호화(SSL/TLS로 안전하게 전송)</span></b></span></td></tr><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>포트 번호</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>80</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>443</b></span></td></tr><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>데이터 암호화</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>없음</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>데이터 암호화 지원</b></span></td></tr><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>속도</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>상대적으로 빠름</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>암호화 처리로 인해 약간 느림</b></span></td></tr><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>사용 사례</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>민감하지 않은 일반 데이터 전송</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>로그인, 결제, 민감, 데이터 전송</b></span></td></tr><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>URL 형식</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>http://</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>https://</b></span></td></tr></tbody></table>

* * *

#### **HTTPS 작동 방식**

-   **SSL/TLS Handshake**  
    **클라이언트와 서버 간 암호화된 통신을 설정하기 위한 초기 과정**
-   **서버가 클라이언트에 SSL 인증서를 전달**  
    **클라이언트는 인증서의 유효성을 확인하고 암호화 키를 교환**
-   **데이터 전송**  
    **데이터는 암호화된 상태로 전송**  
    **클라이언트와 서버는 암호화된 데이터를 복호화해 실제 데이터를 처리**

* * *

#### **HTTP 장/단점**

-   **HTTP 장점**  
    **간단하고 빠르며, 설정이 쉽다**  
    **민감하지 않은 데이터(일반 컨텐츠) 전송에는 충분**
-   **HTTP 단점**  
    **보안 취약점 : 데이터 도청, 변조, 피싱 공격에 취약**

#### **HTTPS 장/단점**

-   **HTTPS 장점**  
    **높은 보안성 : 암호화 및 서버 인증으로 안전한 통신**  
    **SEO(검색엔진 최적화)  : HTTPS 사용 사이트가 검색엔진에서 더 높은 순위를 받을 가능성이 높음**
-   **HTTPS 단점**  
    **설정 복잡 : SSL 인증서를 설치해야 함**  
    **비용 문제 : 유로 인증서 사용 시 비용 발생**  
    **약간의 속도 저하**

* * *

#### **HTTPS 사용 이유**

1.  **보안성강화**  
    **사용자 데이터를 보호하고 신뢰를 제공**  
    **MITM(중간자 공격) 방지**
2.  **SEO 및 사용자 신뢰**  
    **브라우저(Chrome 등) HTTP 사이트를 안전하지 않음으로 표시**  
    **HTTPS를 사용하는 웹사이트는 신뢰도를 높인다**
3.  **필수적인 환경**  
    **로그인 시스템, 전자 상거래, 결제 시스템 등에서 HTTPS는 필수**

#### **결론**

**HTTP는 빠르고 간단하지만, 보안에 민감하지 않은 데이터 전송에 적합**

**HTTPS는 보안이 필수적인 환경에서 표준으로 사용되며, SSL/TLS를 통해 안전한 통신을 보장**

**오늘날의 웹 서비스에서는 HTTPS가 기본적으로 권장**

* * *

### **SSL/TLS Handshake**

-   **클라이언트(예:웹 브라우저)와 서버 간에 안전한 통신을 설정하기 위한 초기 과정**
-   **이 과정에서 암호화 키를 교환하고 연결을 인증하며, 이후 데이터를 안전하게 주고받을 수 있는 기반을 마련**

#### **SSL (Secure Sockets Layer)**

-   **보안 소켓 계층이라는 의미로, 인터넷 통신에서 데이터를 암호화하고 안전하게 전송하기 위한 초기 보안 프로토콜**
-   **현재는 TLS로 대체되었지만, 관습적으로 SSL/TLS라고 함께 표기하는 경우가 많음**

#### **TLS (Transport Layer Security)**

-   **전송 계층 보안이라는 의미로, SSL의 업그레이드된 버전**
-   **더 강력한 보안과 최신 암호화 기술을 지원**

![](/assets/img/posts/298/2.png)

* * *

#### **SSL/TLS Handshake 과정**

1.  **Client Hello (클라이언트 인사)**  
    **클라이언트는 서버에 다음 정보를 보낸다**  
    **1) 지원하는 암호화 스위트 목록(예: AES, RSA, SHA 등)**  
    **2) TLS 버전(예 TLS 1.2, TLS1.3 등)**  
    **3) 랜덤 값 (임시 데이터 암호화를 위한 임의 값)**  
    **4) 확장 정보 (서버 이름 표시(SNI) 등)**
2.  **Server Hello (서버 인사)**  
    **서버는 클라이언트의 요청에 응답하며 다음 정보를 보낸다**  
    **1) 선택된 암호화 스위트(서버가 사용할 암호화 알고리즘)**  
    **2) TLS 버전**  
    **3) 랜덤 값(서버에서 생성한 임의 값)**  
    **4) 서버의 디지털 인증서(SSL 인증서, 서버의 신뢰성 증명)**
3.  **인증서 검증**  
    **클라이언트는 서버가 제공한 디지털 인증서를 검증**  
    **인증서는 CA(인증기관)에서 발급된 것으로, 서버의 신뢰성을 보장**  
    **검증항목**  
     **1) 인증서의 유효기간**  
     **2) 인증서의 발급자(신뢰할 수 이는 CA인지 확인)**  
     **3) 서버의 도메인 이름과 일치 여부**  
    **\*\* 인증서가 신뢰할 수 없는 경우, 연결이 중단**
4.  **비밀 키 교환**  
    **클라이언트와 서버는 안전한 암호화 통신을 위해 세션 키를 교환하거나 생성**  
    **TLS에서 사용하는 암호화 방식에 따라 키 교환 방식이 달라진다**  
    **RSA : 서버의 공개키를 사용해 클라이언트가 세션 키를 암호화하여 전송**  
    **ECDHE(Elliptic Curve Diffie-Hellman Ephemeral) : 양측이 임시 키를 생성하고, 이를 교환하여 공유 세션 키 생성**  
    **\> 세션키는 대칭 암호화 방식(빠르고 효율적)에 사용된다**
5.  **Finished 메시지 정송**  
    **클라이언트와 서버는 생성된 세션 키로 Finished 메시지를 암호화해 상대방에게 전송**  
    **이 과정에서 암호화와 복호화가 성공하면 안전한 통신이 가능하다고 판단**
6.  **암호화된 데이터 전송 시작**  
    **SSL/TLS Handshake가 완료되면, 이후의 통신은 세션 키를 사용해 데이터를 암호화하고 복호화**  
    **세션 키는 대칭 암호화 방식으로 데이터 전송 속도가 빠름**

* * *

#### **SSL/TLS Handshake 다이어그램**

```bash
Client: ----------- Client Hello --------------> Server

Client: <---------- Server Hello -------------- Server

Client: <------- Certificate, Key ----------- Server

Client: -------- Key Exchange & Verify ------> Server

Client/Server: ----- Finished Messages ------ Client/Server
```

**Certificate, Key 단계에서 서버는 인증서와 함께 공개키를 클라이언트에 전달**

**인증서는 CA에 의해 발급된 것으로, 서버의 공개키가 포함되어 있다**

**공개키는 암호화되지 않은 상태로 클라이언트에 전송**

**클라이언트는 서버로부터 받은 인증서를 검증**

**인증서가 유효하다면 서버의 공개키 사용**

**Key Exchange & Verify 단계에서는 클라이언트는 서버의 공개키를 사용해 암호화된 데이터를 생성**

**이 데이터는 클라이언트가 생성한 세션 키를 포함하거나 관련 데이터를 암호화한다**

**이 데이터를 서버로 전송하며, 서버는 자신만이 알고 있는 비공개키로 이를 복호화해 세션 키를 얻는다**

* * *

#### **핵심 개념**

-   **디지털 인증서**  
     **서버 신뢰성을 검증하는 문서**  
     **공개 키와 함께 제공**  
-   **암호화 스위트**  
     **Handshake에 사용할 암호화 알고리즘 집합**  
     **예 : AES(암호화), SHA(해싱), RSA/ECDHE(키 교환)**
-   **세션 키**  
     **클라이언트와 서버 간 데이터를 암호화하는 데 사용되는 대칭 키**  
     **Handshake 과정에서 안전하게 생성 또는 교환**

* * *

#### **SSL/TLS Handshake의 주요 목표**

1.  **보안 채널 설정**  
    **클라이언트와 서버 간에 데이터가 암호화된 상태로 안전하게 전송되도록 보장**
2.  **서버 인증**  
    **클라이언트가 신뢰할 수 있는 서버인지 확인**
3.  **데이터 무결성**  
    **전송 중 데이터가 손상되거나 변조되지 않도록 보호**

* * *

#### **SSL/TLS Handshake 과정 (요약)**

1.  **서버 -> 클라이언트**  
    **서버가 공개키가 포함된 인증서(Certificate)를 클라이언트에게 전송합니다.**  
    **인증서에는 서버의 신원을 증명하는 정보와 공개키가 포함되어 있습니다.**
2.  **클라이언트 인증서 확인**  
    **클라이언트는 인증서를 확인하여 신뢰할 수 있는지 검증합니다.**  
    **(인증서를 발급한 CA를 신뢰하는지, 인증서가 만료되지 않았는지 등을 확인)**
3.  **클라이언트 -> 서버**  
    **클라이언트는 서버의 공개키를 사용해 세션키(Session Key)를 암호화하고, 이를 서버로 전송합니다.**  
    **(이 세션키는 이후 대칭 암호화에 사용됩니다.)**
4.  **서버 세션키 복호화**  
    **서버는 자신의 비공개키를 사용해 클라이언트가 전송한 세션키를 복호화합니다.**  
    **이로써 서버와 클라이언트는 동일한 세션키를 공유하게 됩니다.**
5.  **세션키로 통신 시작**  
    **이후 클라이언트와 서버는 세션키를 사용해 대칭 암호화를 통해 데이터를 암호화하고 복호화합니다.**
