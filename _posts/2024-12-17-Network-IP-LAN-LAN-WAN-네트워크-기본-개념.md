---
title: "[Network] IP, LAN, WAN 네트워크 기본 개념"
date: 2024-12-17 15:58:40 +0900
categories: ["Solution & Tools", "NetWork"]
tags: ["ip", "ip 서브넷", "lan", "subnet mask", "subnet subnetting", "wan 네트워크 기본 개념", "게이트웨이", "라우팅 라우트", "서브넷 서브네팅 서브넷 마스크", "이더넷"]
tistory_url: "https://aroma-bok.tistory.com/entry/Network-IP-LAN-LAN-WAN-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EA%B8%B0%EB%B3%B8-%EA%B0%9C%EB%85%90"
---
### **IP (Internet Protocol)**

-   **인터넷 통신을 가능하게 하는 국제 표준 규약**
-   **IPv4(32bit 주소 체계), IPv6(128bit 주소체계) 2개의 버전 존재**
-   **TCP/IP 네트워크에서 호스트를 고유하게 식별**

![](/assets/img/posts/281/1.png)

*IPv4 - 32bit 주소체계*

![](/assets/img/posts/281/2.png)

*IPv6 - 128bit 주소체계*

### **IP의 클래스**

-   **IP 주소(IPv4)는 네트워크를 구분하고, 네트워크 안의 장치를 식별하기 위해 사용**
-   **IPv4 주소는 32비트로 구성되며, 8비트 4개의 옥텟(Octet)으로 나눔**
-   **IP 클래스는 이러한 IP 주소를 주소 범위와 용도에 따라 분류**

![](/assets/img/posts/281/3.png)

<table style="border-collapse: collapse; width: 100%; height: 84px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 21px;"><td style="width: 50%; height: 21px;">&nbsp;</td><td style="width: 50%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>IP 범위</b></span></td></tr><tr style="height: 21px;"><td style="width: 50%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>A클래스</b></span></td><td style="width: 50%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>1 ~ 126&nbsp;<br></b></span></td></tr><tr style="height: 21px;"><td style="width: 50%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>B클래스</b></span></td><td style="width: 50%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>128 ~ 191</b></span></td></tr><tr style="height: 21px;"><td style="width: 50%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>C클래스</b></span></td><td style="width: 50%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>192 ~ 223</b></span></td></tr></tbody></table>

> **모든 주소의 시작은 네트워크 주소로 사용되고, 마지막은 브로드캐스트 주소로 사용되기 때문에 0, 127은 제외**

* * *

### **LAN(Local Area Network)**

-   **근거리 통신망**
-   **지역화된 영역으로 제한된 네트워크**

### **WAN(Wide Area Network)**

-   **먼 거리에 있는 컴퓨터 그룹을 연결하는 대규모 컴퓨터 네트워크**
-   **인터넷 자체도 WAN으로 간주**
-   **LAN은 WAN을 통해서 연결**

> **TCP/IP**  
> **패킷 통신 방식의 인터넷 프로토콜의 IP와 전송 조절 프로토콜인 TCP로 이루어져 있다.**  
> **IP는 패킷 전달 여부를 보증하지 않고, 패킷을 보낸 순서와 받는 순서가 다를 수 있다.**  
> **TCP는 IP 위에서 동작하는 프로토콜로, 데이터의 전달을 보증하고 보낸 순서대로 받게 해 준다.**  
> **HTTP, FTP, SMTP 등 TCP를 기반으로 한 많은 수의 애플리케이션 프로토콜들이 IP 위에서 동작하기 때문에**  
> **묶어서 TCP/IP로 부른다.**  
>   
> **TCP(Transmission Control Protocol)**  
> **\- 클라이언트와 서버 간에 데이터를 신뢰성 있게 전달하기 위해 만든 프로토콜**  
> **\- LAN, WAN 인트라넷, 인터넷 등 컴퓨터에서 실행되는 프로그램 간에 일련의 데이터를 교환할 수 있게 해 준다**

### **LAN 카드(Local Area Network Card)**

-   **컴퓨터와 네트워크를 연결하는 하드웨어 장치**
-   **컴퓨터 LAN에 연결되도록 해주는 중요한 역할**
-   **일반적으로 이더넷 케이블을 통해 네트워크에 연결되며, IP 주소를 이용해 다른 장치와 통신**
-   **LAN 영역에서 사용하는 표준 통신 기술**
-   **LAN 카드는 Network Interface Controller(NIC)라고도 한다**
-   **이더넷 어댑터**

![](/assets/img/posts/281/4.png)

*LAN / WAN*

* * *

### **서브넷(Subnet)**

-   **작은 네트워크 단위로 나눈 것**

### **서브넷팅(Subnetting)**

-   **네트워크를 더욱 작은 단위의 네트워크(서브넷)로 분할하는 것**
-   **IP 주소의 낭비를 방지**
-   **브로드캐스트 도메인의 크기를 줄여서 성능 향상이 목적**

### **서브넷 마스크(Subnet Mask)**

-   **IP 주소 체계의 Network ID와 Host ID를 분리하는 역할**
-   **IP 주소에서 네트워크와 호스트를 구분하는 데 사용되는 32비트 숫자**
-   **서브넷 마스크가 255.255.255.0이라면, 11111111.11111111.11111111.00000000로 표현**

![](/assets/img/posts/281/5.png)

* * *

### **이더넷(Ethernet)이란?**

-   **로컬 영역 네트워크(LAN)에서 데이터를 전송하는 통신 프로토콜**
-   **컴퓨터 네트워크 기술의 한 표준**
-   **유선 네트워크에서 데이터를 전송하는 방식을 정의**

* * *

### **게이트웨이(GateWay)**

-   **서로 다른 네트워크 간의 통신을 가능하게 하는 중계 장치 or 노드**
-   **소프트웨어 측을 강조할 때, 게이트웨이라 하며 하드웨어적 측면을 강조할 때, 라우터라 한다**
-   **NAT(Network Address Translation) 기술을 사용하여 내 IP 주소를 공인 IP 주소로 변환**

### **라우팅(Routing)**

-   **데이터를 한 네트워크에서 다른 네트워크로 전달하기 위해 최적의 경로를 선택하고 지정하는 과정**

![](/assets/img/posts/281/6.jpg)
