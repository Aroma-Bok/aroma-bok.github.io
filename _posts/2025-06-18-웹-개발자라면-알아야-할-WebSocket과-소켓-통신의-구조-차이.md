---
title: "웹 개발자라면 알아야 할 WebSocket과 소켓 통신의 구조 차이"
date: 2025-06-18 13:01:30 +0900
categories: ["ETC", "IT Knowledge"]
tags: ["websocket", "소켓", "소켓통신", "소켓통신이란", "웹소켓 소켓 차이점"]
tistory_url: "https://aroma-bok.tistory.com/entry/%EC%9B%B9-%EA%B0%9C%EB%B0%9C%EC%9E%90%EB%9D%BC%EB%A9%B4-%EC%95%8C%EC%95%84%EC%95%BC-%ED%95%A0-WebSocket%EA%B3%BC-%EC%86%8C%EC%BC%93-%ED%86%B5%EC%8B%A0%EC%9D%98-%EA%B5%AC%EC%A1%B0-%EC%B0%A8%EC%9D%B4"
---
#### **1\. 소켓의 본질 – “연결된 통신 통로”**

-   **소켓(Socket)은 네트워크에서 \*\*두 컴퓨터가 데이터를 주고받기 위해 만든 연결 지점(Connection Endpoint)\*\***  
    **즉, 양쪽 컴퓨터가 소켓을 만들어 연결되면, 그 위에서 텍스트든 JSON이든 HTTP든 마음껏 주고받을 수 있습니다.**

#### **2. 어떤 상황에서 쓰이는가?**

**HTTP 요청도 결국 소켓 위에서 동작**

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft"><tbody><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>계층</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>예</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>설명</b></span></td></tr><tr><td style="width: 33.3333%;"><b><span style="font-family: 'Noto Serif KR';">Application<span>&nbsp;</span></span></b></td><td style="width: 33.3333%;"><b><span style="font-family: 'Noto Serif KR';">HTTP,&nbsp;FTP<span>&nbsp;</span></span></b></td><td style="width: 33.3333%;"><span><b><span style="font-family: 'Noto Serif KR';">우리가 작성하는 웹 API, 브라우저 요청</span></b></span></td></tr><tr><td style="width: 33.3333%;"><b><span style="font-family: 'Noto Serif KR';">Transport<span>&nbsp;</span></span></b></td><td style="width: 33.3333%;"><b><span style="font-family: 'Noto Serif KR';">TCP&nbsp;/&nbsp;UDP</span></b></td><td style="width: 33.3333%;"><b><span style="font-family: 'Noto Serif KR';">💡</span></b> <b><span style="font-family: 'Noto Serif KR';">여기서&nbsp;소켓이&nbsp;사용됨!</span></b></td></tr><tr><td style="width: 33.3333%;"><b><span style="font-family: 'Noto Serif KR';">Network<span>&nbsp;</span></span></b></td><td style="width: 33.3333%;"><b><span style="font-family: 'Noto Serif KR';">IP<span>&nbsp;</span></span></b></td><td style="width: 33.3333%;"><span><b><span style="font-family: 'Noto Serif KR';">인터넷 주소와 라우팅</span></b></span></td></tr><tr><td style="width: 33.3333%;"><b><span style="font-family: 'Noto Serif KR';">Physical<span>&nbsp;</span></span></b></td><td style="width: 33.3333%;"><b><span style="font-family: 'Noto Serif KR';">이더넷</span></b></td><td style="width: 33.3333%;"><b><span style="font-family: 'Noto Serif KR';">Wi-Fi 실제&nbsp;전송&nbsp;매체</span></b></td></tr></tbody></table>

**즉, HTTP 요청은 실제로는 TCP/IP 연결이 먼저 맺어지고 → 그 위에서 메시지가 오가는 구조**

#### **3\. Java에서 소켓을 직접 쓰는 경우**

-   **Spring Web MVC나 Boot를 쓰면 소켓을 직접 다루는 경우는 거의 없다.**
-   **하지만 다음과 같은 상황에서는 직접 다루기도 한다.**

**예시 1: 채팅 서버 만들기 (TCP 소켓)**

```java
ServerSocket serverSocket = new ServerSocket(9999);
Socket socket = serverSocket.accept(); // 클라이언트 연결 대기
InputStream in = socket.getInputStream();  // 메시지 받기
OutputStream out = socket.getOutputStream();  // 메시지 보내기
```

-   **여기서 socket.getInetAddress() → 상대방 IP**
-   **getInputStream() → 받은 메시지 읽기**
-   **getOutputStream() → 메시지 보내기**

**예시 2: WebSocket (양방향 웹 통신)**

-   **실시간 채팅, 주식 차트, 알림 등에 쓰이는 HTTP 업그레이드 방식**
-   **HTTP → WebSocket 프로토콜로 전환되면, 내부적으로 소켓을 유지하며 양방향 통신**

#### **4\. Spring에서는 소켓을 어떻게 다루는가?**

<table style="border-collapse: collapse; width: 100%; height: 93px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 21px;"><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">용도</span></b></td><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">기술<span>&nbsp;</span></span></b></td><td style="width: 33.3333%; height: 21px;"><span><b><span style="font-family: 'Noto Serif KR';">소켓 관련</span></b></span></td></tr><tr style="height: 21px;"><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">일반&nbsp;웹&nbsp;요청</span></b></td><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">Spring&nbsp;MVC&nbsp;(@RestController)</span></b></td><td style="width: 33.3333%; height: 21px;"><span><b><span style="font-family: 'Noto Serif KR';">소켓 직접 사용 X<br>(getRemoteAddr()로만 조회 가능)</span></b></span></td></tr><tr style="height: 17px;"><td style="width: 33.3333%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">실시간&nbsp;알림</span></b></td><td style="width: 33.3333%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">Spring&nbsp;WebSocket&nbsp;/&nbsp;STOMP</span></b></td><td style="width: 33.3333%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">내부적으로&nbsp;소켓&nbsp;연결&nbsp;유지</span></b></td></tr><tr style="height: 17px;"><td style="width: 33.3333%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">TCP&nbsp;서버</span></b></td><td style="width: 33.3333%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">Netty,&nbsp;Reactor&nbsp;Netty</span></b></td><td style="width: 33.3333%; height: 17px;"><span><b><span style="font-family: 'Noto Serif KR';">Non-blocking 소켓 처리</span></b></span></td></tr><tr style="height: 17px;"><td style="width: 33.3333%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">HTTP&nbsp;서버</span></b></td><td style="width: 33.3333%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">Spring&nbsp;Boot&nbsp;(Tomcat,&nbsp;Jetty)</span></b></td><td style="width: 33.3333%; height: 17px;"><span><b><span style="font-family: 'Noto Serif KR';">WAS가 소켓 연결을 관리함</span></b></span></td></tr></tbody></table>

**우리가 직접 소켓을 열고 accept() 하는 일은 거의 없음.**

**대신 WAS(예: Tomcat)가 그걸 대신해 주고, 우리는 요청 객체만 다룬다.**

* * *

#### **개발에서 "소켓 통신을 한다"라는 말은 무슨 의미일까?**

1.  **서버와 클라이언트가 HTTP 없이, 혹은 HTTP를 넘어서, 직접 데이터를 주고받는 방식  
    즉, HTTP 요청-응답 모델과 달리, 소켓을 유지한 채로 실시간으로 데이터를 주고받는 구조  
    **
2.  **HTTP 같은 고수준 통신 방식이 아니라, 개발자가 직접 네트워크 연결을 관리하면서 데이터를 주고받는 방식**  
    **즉, 소켓 통신”이란 = 클라이언트와 서버가 직접 연결된 통로(TCP/IP)를 통해 데이터를 주고받는 통신을 한다는 뜻**

#### **소켓 통신은 언제 쓰이는가?**

-   **브라우저 → 서버 요청: HTTP 기반, 우리가 흔히 쓰는 방식**
-   **반면 다음과 같은 경우엔 소켓 통신이라 말한다.**

<table style="border-collapse: collapse; width: 100%; height: 126px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 21px;"><td style="width: 50%; height: 21px;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>상황</b></span></td><td style="width: 50%; height: 21px;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>왜 소켓 통신을 쓰나?</b></span></td></tr><tr style="height: 21px;"><td style="width: 50%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>실시간 채팅</b></span></td><td style="width: 50%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>사용자의 말이 실시간으로 전달되어야 하니까</b></span></td></tr><tr style="height: 21px;"><td style="width: 50%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>게임 서버</b></span></td><td style="width: 50%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>위치, 움직임 등 빠르게 전송돼야 해서</b></span></td></tr><tr style="height: 21px;"><td style="width: 50%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>IoT 기기 통신</b></span></td><td style="width: 50%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>기기 ↔ 서버가 직접 연결되어야 해서</b></span></td></tr><tr style="height: 21px;"><td style="width: 50%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>파일 전송</b></span></td><td style="width: 50%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>FTP 등은 소켓 위에서 데이터 스트림 직접 주고받음</b></span></td></tr><tr style="height: 21px;"><td style="width: 50%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>서버 간 통신 (예: 마이크로서비스)</b></span></td><td style="width: 50%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>HTTP가 아닌 TCP로 성능 최적화할 때</b></span></td></tr></tbody></table>

#### **HTTP vs 소켓 통신 - 개발자들 용어차이**

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft"><tbody><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>표현</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>실질 의미</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>설명</b></span></td></tr><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>"HTTP 요청 보낸다"</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>브라우저 → 서버, API 호출</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>고수준, stateless</b></span></td></tr><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>"소켓 통신한다"</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>양방향 실시간 통신, TCP 직렬 연결</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>저수준, 연결유지</b></span></td></tr><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>"WebSocket 통신"</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>HTTP → 소켓으로 업그레이드</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>채팅, 알림 등에 적합</b></span></td></tr><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>"소켓 서버 만든다"</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>ServerSocket 등 직접 구현</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>게임 IoT, 커스터마이징 목적</b></span></td></tr></tbody></table>

**즉, "소켓 통신 한다"라는 의미는**

**"브라우저에서 HTTP 요청 보내는 것처럼 단방향 요청/응답이 아니라,**

**서버랑 클라이언트가 직접 연결된 상태에서 실시간으로 데이터를 주고받는 구조(양방향)를 쓴다"는 뜻**

#### **요약**

<table style="border-collapse: collapse; width: 100%; height: 94px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 22px;"><td style="width: 50%; height: 22px;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>개념</b></span></td><td style="width: 50%; height: 22px;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>설명</b></span></td></tr><tr style="height: 21px;"><td style="width: 50%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">소켓&nbsp;</span></b></td><td style="width: 50%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">연결된 양방향 데이터 통로</span></b></td></tr><tr style="height: 17px;"><td style="width: 50%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">소켓&nbsp;통신&nbsp;</span></b></td><td style="width: 50%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">소켓을 통해 직접 데이터 송수신 (보통 TCP/IP)</span></b></td></tr><tr style="height: 17px;"><td style="width: 50%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">" 소켓 통신을 한다"</span></b></td><td style="width: 50%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">HTTP처럼 한 번 요청해서 끝나는 방식이 아니라,<br>지속적인 연결을 통해 데이터를 주고받는다는 의미</span></b></td></tr><tr style="height: 17px;"><td style="width: 50%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">WebSocket&nbsp;</span></b></td><td style="width: 50%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">HTTP를 소켓처럼 바꿔주는 프로토콜. 소켓 통신의 한 형태</span></b></td></tr></tbody></table>

* * *

#### **WebSocket도 소켓인데 뭐가 다르지?**

-   **둘은 관련 있지만 엄연히 다른 계층의 기술입니다.**
-   **\*\*소켓(Socket)\*\*은 통신을 위한 "저수준 기술"이고,**
-   **WebSocket은 이를 웹 환경에서 실시간 양방향 통신을 하기 위해 만든 고수준 프로토콜**

<table style="border-collapse: collapse; width: 100%; height: 189px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 21px;"><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">구분<span>&nbsp;</span></span></b></td><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">WebSocket<span>&nbsp;</span></span></b></td><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">소켓 (TCP 소켓)</span></b></td></tr><tr style="height: 21px;"><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">계층<span>&nbsp;</span></span></b></td><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">Application&nbsp;Layer</span></b></td><td style="width: 33.3333%; height: 21px;"><span><b><span style="font-family: 'Noto Serif KR';">Transport Layer</span></b></span></td></tr><tr style="height: 21px;"><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">프로토콜&nbsp;여부</span></b></td><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">✅&nbsp;있음&nbsp;(RFC&nbsp;6455)</span></b></td><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">❌&nbsp;없음&nbsp;(TCP&nbsp;위에서&nbsp;직접&nbsp;구현)</span></b></td></tr><tr style="height: 21px;"><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">사용&nbsp;환경<span>&nbsp;</span></span></b></td><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">브라우저&nbsp;↔&nbsp;서버&nbsp;/&nbsp;JS에서&nbsp;사용&nbsp;가능</span></b></td><td style="width: 33.3333%; height: 21px;"><span><b><span style="font-family: 'Noto Serif KR';">클라이언트 ↔ 서버<br>(프로그램끼리 직접 통신)</span></b></span></td></tr><tr style="height: 21px;"><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">연결&nbsp;방식</span></b></td><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">HTTP&nbsp;→&nbsp;Upgrade&nbsp;→&nbsp;WebSocket</span></b></td><td style="width: 33.3333%; height: 21px;"><span><b><span style="font-family: 'Noto Serif KR';">TCP 연결 직접 생성</span></b></span></td></tr><tr style="height: 21px;"><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">메시지&nbsp;형식</span></b></td><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">프레임&nbsp;단위&nbsp;(텍스트/바이너리)<span>&nbsp;</span></span></b></td><td style="width: 33.3333%; height: 21px;"><span><b><span style="font-family: 'Noto Serif KR';">바이트 스트림 직접 전송</span></b></span></td></tr><tr style="height: 21px;"><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">일반&nbsp;용도</span></b></td><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">실시간&nbsp;웹앱&nbsp;(채팅,&nbsp;알림,&nbsp;협업&nbsp;등)<span>&nbsp;</span></span></b></td><td style="width: 33.3333%; height: 21px;"><span><b><span style="font-family: 'Noto Serif KR';">게임, 채팅, IoT, 금융 시스템 등</span></b></span></td></tr><tr style="height: 21px;"><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">브라우저&nbsp;지원</span></b></td><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">✅&nbsp;지원&nbsp;(JavaScript&nbsp;API&nbsp;존재)<span>&nbsp;</span></span></b></td><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">❌&nbsp;브라우저에서는&nbsp;직접&nbsp;사용&nbsp;불가</span></b></td></tr><tr style="height: 21px;"><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">표준화</span></b></td><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';"><span>&nbsp;</span>W3C + RFC로 명세화</span></b></td><td style="width: 33.3333%; height: 21px;"><b><span style="font-family: 'Noto Serif KR';">OS/네트워크&nbsp;수준&nbsp;표준&nbsp;(BSD&nbsp;소켓&nbsp;등)</span></b></td></tr></tbody></table>

**WebSocket은** **초기에 HTTP 요청을 보내서 연결을 시작한 뒤,**

**연결이 성립되면 TCP 소켓 위에서 독립적인 양방향 채널을 형성**

**즉, WebSocket은 TCP 소켓 위에서 동작하는 '웹 친화적' 프로토콜**

**클라이언트**

```java
const socket = new WebSocket("ws://localhost:8080/chat");
socket.onmessage = (e) => console.log("메시지:", e.data);
```

**서버(Spring)**

```java
@MessageMapping("/chat")
@SendTo("/topic/messages")
public Message handle(Message message) {
    return message;
}
```

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft"><tbody><tr><td style="width: 26.3566%;"><b><span style="font-family: 'Noto Serif KR';">항목<span>&nbsp;</span></span></b></td><td style="width: 40.31%;"><b><span style="font-family: 'Noto Serif KR';">WebSocket<span>&nbsp;</span></span></b></td><td style="width: 33.3333%;"><span><b><span style="font-family: 'Noto Serif KR';">TCP 소켓</span></b></span></td></tr><tr><td style="width: 26.3566%;"><b><span style="font-family: 'Noto Serif KR';">동작&nbsp;방식</span></b></td><td style="width: 40.31%;"><b><span style="font-family: 'Noto Serif KR';">HTTP&nbsp;업그레이드&nbsp;후&nbsp;TCP&nbsp;소켓&nbsp;위에서&nbsp;통신<span>&nbsp;</span></span></b></td><td style="width: 33.3333%;"><span><b><span style="font-family: 'Noto Serif KR';">TCP 직접 연결</span></b></span></td></tr><tr><td style="width: 26.3566%;"><b><span style="font-family: 'Noto Serif KR';">추상화&nbsp;수준</span></b></td><td style="width: 40.31%;"><b><span style="font-family: 'Noto Serif KR';">고수준&nbsp;(메시지&nbsp;기반,&nbsp;웹용)</span></b></td><td style="width: 33.3333%;"><span><b><span style="font-family: 'Noto Serif KR';">저수준 (바이트 스트림 기반)</span></b></span></td></tr><tr><td style="width: 26.3566%;"><b><span style="font-family: 'Noto Serif KR';">관계<span>&nbsp;</span></span></b></td><td style="width: 40.31%;"><b><span style="font-family: 'Noto Serif KR';">WebSocket은&nbsp;TCP&nbsp;소켓&nbsp;위에서&nbsp;동작하는&nbsp;<br>특수한&nbsp;프로토콜</span></b></td><td style="width: 33.3333%;"><b><span style="font-family: 'Noto Serif KR';">WebSocket의&nbsp;기반이&nbsp;되는&nbsp;전송&nbsp;계층</span></b></td></tr></tbody></table>

#### **정리**

-   **Socket과 WebSocket은 모두 포트를 통한 양방향 통신**
-   **WebSocket은 브라우저에서도 쉽게 사용 가능하도록 TCP 위에 HTTP 업그레이드 프로토콜로 설계된 응용계층 프로토콜**
-   **TCP 소켓은 더 저수준이며, 더 넓은 환경(앱, 서버, IoT 등)에서 직접 통제할 수 있는 반면, WebSocket은 웹 친화적**

* * *

#### **언제 소켓(Socket)을 사용하는가?**

<table style="border-collapse: collapse; width: 100%; height: 110px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 22px;"><td style="width: 50%; height: 22px;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>조건</b></span></td><td style="width: 50%; height: 22px;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>설명</b></span></td></tr><tr style="height: 22px;"><td style="width: 50%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>웹 환경이 아니다</b></span></td><td style="width: 50%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>브라우저를 거치지 않고, 앱, 기기, 서버 간 통신</b></span></td></tr><tr style="height: 22px;"><td style="width: 50%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>고성능, 고속 전송</b></span></td><td style="width: 50%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>지연시간 최소화가 필요할 때 (ex. 게임, IoT)</b></span></td></tr><tr style="height: 22px;"><td style="width: 50%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>커스텀 프로토콜 필요</b></span></td><td style="width: 50%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>HTTP/프레임 기반 아닌 고유 프로토콜 구현이 필요할 때</b></span></td></tr><tr style="height: 22px;"><td style="width: 50%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>경량성 중요</b></span></td><td style="width: 50%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>WebSocket보다 패킷 오버헤드가 적어야 할 때<br>(IoT, 임베디드 등)</b></span></td></tr></tbody></table>

#### **언제 WebSocket을 사용하는가?**

<table style="border-collapse: collapse; width: 100%; height: 110px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 22px;"><td style="width: 50%;height: 22px;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>조건</b></span></td><td style="width: 50%;height: 22px;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>설명</b></span></td></tr><tr style="height: 22px;"><td style="width: 50%;height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>브라우저에서 실시간 통신이 필요</b></span></td><td style="width: 50%;height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>JS로 쉽게 연결 가능 (new WebSocket())</b></span></td></tr><tr style="height: 22px;"><td style="width: 50%;height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>HTTP 기반 네트워크 환경 사용</b></span></td><td style="width: 50%;height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>프록시, 방화벽, HTTPS 등을 그대로 활용 가능</b></span></td></tr><tr style="height: 22px;"><td style="width: 50%;height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>양방향 실시간 통신이 필요</b></span></td><td style="width: 50%;height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>채팅, 알림, 협업, 실시간 알림 등</b></span></td></tr><tr style="height: 22px;"><td style="width: 50%;height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>인프라 구성의 복잡도를 줄이고 싶을 때</b></span></td><td style="width: 50%;height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>HTTP 업그레이드 방식이라 기존 인프라 활용 가능</b></span></td></tr></tbody></table>

#### **핵심 비교 정리**

<table style="border-collapse: collapse; width: 100%; height: 146px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 22px;"><td style="width: 33.3333%; height: 22px;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>구분</b></span></td><td style="width: 33.3333%; height: 22px;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>TCP/UDP 소켓</b></span></td><td style="width: 33.3333%; height: 22px;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>WebSocket</b></span></td></tr><tr style="height: 22px;"><td style="width: 33.3333%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>연결 방식</b></span></td><td style="width: 33.3333%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>TCP/UDP 직접 연결</b></span></td><td style="width: 33.3333%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>HTTP → Upgrade → TCP</b></span></td></tr><tr style="height: 17px;"><td style="width: 33.3333%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>프로토콜 추상화</b></span></td><td style="width: 33.3333%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>없음(직접 구현)</b></span></td><td style="width: 33.3333%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>있음 (프레임 구조, ping/pong 등)</b></span></td></tr><tr style="height: 17px;"><td style="width: 33.3333%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>브라우저 지원</b></span></td><td style="width: 33.3333%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>X</b></span></td><td style="width: 33.3333%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>O</b></span></td></tr><tr style="height: 17px;"><td style="width: 33.3333%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>성능</b></span></td><td style="width: 33.3333%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>빠름 (특히 UDP)</b></span></td><td style="width: 33.3333%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>Web보다 빠름, 소켓보다 느릴 수 있음</b></span></td></tr><tr style="height: 17px;"><td style="width: 33.3333%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>보안 전송</b></span></td><td style="width: 33.3333%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>SSL/TLS 직접 구성 필요</b></span></td><td style="width: 33.3333%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>wss:// 사용 가능</b></span></td></tr><tr style="height: 17px;"><td style="width: 33.3333%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>인프라/방화벽 통과</b></span></td><td style="width: 33.3333%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>어려움 (포트 열어야 함)</b></span></td><td style="width: 33.3333%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>용이 (80/443 포트 그대로 사용)</b></span></td></tr><tr style="height: 17px;"><td style="width: 33.3333%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>개발 편의성</b></span></td><td style="width: 33.3333%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>어려움 (패킷 조작, 오류처리 등 직접구현)</b></span></td><td style="width: 33.3333%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>쉬움 (JS에서도 한 줄로 연결)</b></span></td></tr></tbody></table>
