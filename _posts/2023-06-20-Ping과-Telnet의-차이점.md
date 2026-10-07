---
title: "Ping과 Telnet의 차이점"
date: 2023-06-20 15:10:20 +0900
categories: ["Solution & Tools", "NetWork"]
tags: ["ping", "ping telnet 차이", "telnet", "핑 텔넷"]
tistory_url: "https://aroma-bok.tistory.com/entry/Ping%EA%B3%BC-Telnet%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90"
---
### **Ping**

**Ping은 호스트나 네트워크 장치의 가용성을 확인하기 위해 사용됩니다.**

**ICMP(Internet Control Message Protocol)를 사용하여 목적지 호스트로 작은 패킷을 보내고 응답을 확인함으로써**

**목적지 호스트에 대한 응답 시간과 가용성을 측정합니다.**

**사용법 : ping \[목적지 서버 IP\]**

#### **ICMP**

**Internet Control Message Protocol - 인터넷 제어 메시지 프로토콜의 줄임말**  
**이 프로토콜은 주로 네트워크 장치의 상태, 오류 및 도달 불가능한 상태 등을 알리는 데 사용됩니다.**

* * *

### **Telnt**

**Telnet은 원격 호스트 또는 장치에 로그인하고 원격으로 명령을 실행하기 위해 사용됩니다.**

**TCP 프로토콜을 사용하여 목적지 호스트에 연결합니다.**

**사용법 : telnet \[목적지 서버 IP\] \[서비스 Port\]**

**Telnet으로 원격 로그인 목적으로 사용하게 된다면, 데이터를 암호화하지 않고 전송해서 보안상 취약합니다.**  
**때문에, SSH(Secure Shell)이 그 역할을 많이 대체하고 있습니다.**

#### **SSH**

**원격 호스트에 접속하기 위해 사용되는 보안 프로토콜**  
**사용자(클라이언트)와 서버(호스트)는 각각의 키를 보유하고 있으며, 이 키를 이용해 연결 상대를 인증하고** 

**안전하게 데이터를 주고받습니다.**

* * *

**요약하면, Ping은 네트워크 통신이 가능한지 확인**

**Telnet은 목적지 서버의 특정 서비스가 살아 있는지 확인하는 용도로 많이 사용**
