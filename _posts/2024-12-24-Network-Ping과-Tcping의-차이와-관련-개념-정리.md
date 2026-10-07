---
title: "[Network] Ping과 Tcping의 차이와 관련 개념 정리"
date: 2024-12-24 17:52:45 +0900
categories: ["Solution & Tools", "NetWork"]
tags: ["ping", "ping vs tcping", "ping 개념", "ping이란", "tcping", "tcping 사용", "tcping이란"]
tistory_url: "https://aroma-bok.tistory.com/entry/Network-Ping%EA%B3%BC-Tcping%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%99%80-%EA%B4%80%EB%A0%A8-%EA%B0%9C%EB%85%90-%EC%A0%95%EB%A6%AC"
---
### **Ping과 Tcping의 차이와 관련 개념 정리**

#### **Ping**

-   **목적**  
    **네트워크 연결 상태 확인**
-   **작동 원리**  
    **ICMP(Internet Control Message Protocol) 사용**  
    **특정 호스트에 ICMP Echo Request 패킷을 보내고, Echo Reply를 기다림**
-   **사용 사례**  
    **서버나 장치가 네트워크 상에서 활성 상태인지 확인**  
    **네트워크 연결 지연 시간(Latency) 측정**
-   **한계**  
    **ICMP가 차단된 네트워크에서는 동작하지 않음**  
    **TCP나 UDP 연결 상태를 직접 확인하지 않음**

#### **Tcping**

-   **목적**  
    **TCP 포트를 통한 연결 상태 확인**
-   **작동원리**  
    **특정 호스트와 포트 번호에 TCP 연결을 시도**  
    **연결 성공 여부와 지연 시간(Latency)을 측정**
-   **사용사례**  
    **특정 서비스(예: HTTP, FTP, SSH)가 작동 중인지 확인**  
    **방화벽이나 네트워크 설정으로 인해 포트가 차단되었는지 테스트**
-   **장점  
    **ICMP를 사용하지 않으므로 Ping이 차단된 환경에서도 동작  
    **특정 포트 기반 서비스의 연결상태를 직접 확인 가능******
-   ****한계  
    TCP 연결만 지원 (UDP 상태 확인 불가)  
    ****서비스가 실행 중이지만 포트가 열려 있지 않으면 확인 불가****

* * *

#### **Ping 활용**

**서버가 네트워크에 연결되어 있는지 확인**

**지연시간(Latency) 파악**

#### **Tcping 활용**

**웹 서버 (예: 포트 80, 443)가 정상 작동 중인지 확인**

**데이터베이스 서버 (예: 3306)가 가용한 지 확인**

**특정 서비스가 작동 및 가용한 지 확인**

**방화벽 규칙에 의해 포트가 열려 있는지 테스트**

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft"><tbody><tr><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>항목</b></span></td><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>Ping</b></span></td><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>Tcping</b></span></td></tr><tr><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>프로토콜</b></span></td><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>ICMP</b></span></td><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>TCP</b></span></td></tr><tr><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>확인 대상</b></span></td><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>네트워크 연결 상태</b></span></td><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>특정 서비스(포트 기반 서비스) 상태 확인</b></span></td></tr><tr><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>연결 확인 방식</b></span></td><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>Echo Request/Reply</b></span></td><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>TCP 연결 시도</b></span></td></tr><tr><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>지연 시간 측정</b></span></td><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>가능</b></span></td><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>가능</b></span></td></tr><tr><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>차단 여부</b></span></td><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>방화벽에서 ICMP 차단시 동작 안 함</b></span></td><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>ICMP 차단 환경에서도 동작</b></span></td></tr><tr><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>포트 지정</b></span></td><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>불가능</b></span></td><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>가능 (TCP 포트 기반)</b></span></td></tr><tr><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>사용 예</b></span></td><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>네트워크 상태 점검</b></span></td><td style="width: 33.3333%; text-align: left;"><span style="font-family: 'Noto Serif KR';"><b>서비스 상태 점검 (HTTP, DB, SSH 등)</b></span></td></tr></tbody></table>

* * *

### **관련 개념**

#### **ICMP (Internet Control Message Protocol)**

-   **네트워크 상태를 진단하거나 에러 메시지를 전달하는 데 사용되는 프로토콜**
-   **Ping 명령은 ICMP의 Echo Request와 Echo Reply를 사용**

#### **TCP(Transmission Control Protocol)**

-   **신뢰성 있는 데이터 전송을 보장하는 프로토콜**
-   **Tcping은 TCP 연결 과정을 통해 서비스 상태를 확인**

#### **UDP(User Datagram Protocol)**

-   **비연결형 프로토콜로, 빠르지만 신뢰성이 낮음**
-   **Ping이나 Tcping은 UDP 기반 연결 상태를 직접 확인할 수 없음**

#### **방화벽**

-   **네트워크 트래픽을 제어하는 보안 장치**
-   **ICMP나 특정 TCP/UDP 포트를 차단하여 Ping 또는 Tcping이 실패할 수 있음**

#### **포트 (Port)**

-   **네트워크 서비스가 동작하는 가상 통신 채널**
-   **Tcping은 지정된 TCP 포트에 대해 연결 상태를 점검**

* * *

### **Ping은 가는데 Tciping이 안 가는 경우**

#### **  
1\. 서비스 포트가 차단됨**

-   **방화벽에서 특정 TCP 포트를 차단하거나, 서버에서 서비스가 해당 포트를 수신 대기하지 않을 때**
-   **예: 서버는 가동 중이지만 HTTP(포트 80)또는 SSH(포트 22)가 차단된 경우**

#### **2\. 서비스가 실행 중이지 않음**

-   **서버는 활성화되어 있지만, 해당 포트에서 동작해야 하는 애플리케이션이 비활성 상태일 때**

#### **3\. TCP 연결 제한**

-   **서버가 TCP 연결을 너무 많이 수신하여 새로운 연결 요청을 처리할 수 없을 때**

#### **4\. 포트 재지정**

-   **요청한 서비스가 다른 포트에서 동작하고 있어 요청한 포트에 접근이 불가능한 경우**

#### **5\. 서버가 ICMP는 허용하지만 TCP는 제한**

-   **ICMP 패킷(Ping)은 허용되지만, TCP 연결은 방화벽이나 보안 정책에 의해 제한된 경우**

### **  
Tcping은 가는데 Ping이 안 가는 경우**

#### **  
1\. ICMP 패킷 차단**

-   **방화벽, 보안 소프트웨어, 또는 서버 설정에서 ICMP(Ping) 요청을 차단한 경우**
-   **클라우드 환경(예:AWS, Azure 등)에서는 기본적으로 ICMP가 차단된 경우가 많음**

#### **2\. ICMP 비활성화**

-   **서버에서 ICMP 응답을 비화성화하여 Ping 요청에 대해 응답하지 않는 경우**

#### **3\. TCP 트래픽은 허용됨**

-   **TCP 기반 연결(예: HTTP, SSH 등)은 방화벽 설정에서 허용된 경우**

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft"><tbody><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>상황</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>Ping은 가능, Tcping은 불가능</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>Tcping은 가능, Ping은 불가능</b></span></td></tr><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>원인</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>포트 차단, 서비스 비활성화, TCP 제한</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>ICMP 차단, ICMP 비활성화, 보안정책</b></span></td></tr><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>해결 방법</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>포트 열기, 서비스 활성화, 방화벽 규칙 확인</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>ICMP 허용 설정, 방화벽 규칙 확인</b></span></td></tr><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>테스트 가능한 사례</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>네트워크 연결은 OK, 특정 포트 문제 있음</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>서비스 정상, 네트워크 진단 불가</b></span></td></tr></tbody></table>

**둘 다 실패하는 경우에는 서버 다운, 네트워크 연결 문제, 방화벽 완전 차단이 되었는지 확인해봐야 한다**

* * *

#### **OSI 7 계층에 속한 ICMP와 TCP**

#### **ICMP**

-   **계층**  
    **네트워크 계층 (3 계층)**
-   **역할**  
    **네트워크 상태를 진단, 오류 메시지를 전달하기 위한 프로토콜**
-   **특징**  
    **데이터를 전송하지 않으며, 네트워크 문제를 감지하거나 알리는 데 사용**  
    **Ping은 ICMP의 Echo Request/Reply 메시지를 사용**  
    **연결 지향적이지 않으며, 포트 번호를 사용하지 않음**

#### **TCP**

-   **계층**  
    **전송계층(4 계층)**
-   **역할**  
    **신뢰성 있는 데이터 전송을 보장하는 프로토콜**
-   **특징**  
    **연결 지향적 프로토콜로, 3-way handshake 과정을 통해 연결을 설정**  
    **데이터 패킷의 순서 보장 및 오류 제어를 제공**  
    **포트 번호를 사용하여 애플리케이션 간 데이터를 전송**
