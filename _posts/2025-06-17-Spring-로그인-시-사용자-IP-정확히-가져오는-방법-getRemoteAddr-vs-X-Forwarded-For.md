---
title: "Spring 로그인 시 사용자 IP 정확히 가져오는 방법 (getRemoteAddr vs X-Forwarded-For)"
date: 2025-06-17 16:58:27 +0900
categories: ["ETC", "IT Knowledge"]
tags: ["getremoteaddr()", "ip 가져오는 방법", "x-forwarded-for", "x-forwarded-for 와 getremoteaddr() 차이점"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-%EB%A1%9C%EA%B7%B8%EC%9D%B8-%EC%8B%9C-%EC%82%AC%EC%9A%A9%EC%9E%90-IP-%EC%A0%95%ED%99%95%ED%9E%88-%EA%B0%80%EC%A0%B8%EC%98%A4%EB%8A%94-%EB%B0%A9%EB%B2%95-getRemoteAddr-vs-X-Forwarded-For"
---
#### **request.getRemoteAddr()**

-   **요청을 보낸 직접 클라이언트의 IP**
-   **일반적인 방식, 프록시 환경에서는 실제 IP 아님**

#### **StringUtils.getUserIp()**

-   **HTTP 헤더(X-Forwarded-For, Proxy-Client-IP 등)를 기반으로 실제 사용자 IP 추출**
-   **프록시, 로드밸런서 환경 대응용**

* * *

#### **request.getRemoteAddr() 개념**

```java
String ip = request.getRemoteAddr();
```

-   **클라이언트가 서버에 접속할 때의 \*\*소켓(IP)\*\*을 반환**
-   **일반적인 환경에서는 사용자의 공인 IP가 맞음**
-   **하지만 프록시나 로드밸런서를 거칠 경우, 그 장비의 IP가 찍힌다.**
-   ****getRemoteAddr()는 단순하지만, 프록시 환경에선 무의미해질 수 있음****
-   ******네트워크 소켓 수준에서 IP 주소를 가져온다.  
    **HTTP 프로토콜 위가 아닌, TCP/IP 레벨에서 연결된 클라이언트의 IP**  
    ******

#### **StringUtils.getUserIp()는 어떤 방식인가?**

-   **일반적으로 다음과 같은 HTTP 헤더를 순서대로 확인**
-   **X-Forwarded-For: 실제 사용자 IP, 중간 프록시 IP들 (쉼표로 구분)  
    ex) **X-Forwarded-For: 203.0.113.123, 10.1.2.3****
-   ******실제 유저 IP를 파악하기 위해 헤더를 복합적으로 분석하는 방식으로, 운영 환경에서의 신뢰성 확보에** **필수적인  
    보완 로직******
-   ********프록시 서버(Nginx, Cloudflare 등)가 클라이언트의 IP를 대신해서 이 헤더에 담아준다.********
-   ******프록시 환경에서도 실제 유저의 IP를 식별하려는 로직******
-   ********HTTP 클라이언트(또는 중간 프록시)가 조작 가능하므로 신뢰할 수 있는 네트워크에서만 사용해야 한다.********

```java
public static String getUserIp(HttpServletRequest request) {
    String ip = request.getHeader("X-Forwarded-For");
    if (ip != null && ip.contains(",")) {

        ip = ip.split(",")[0]; // 첫 번째 IP가 실제 클라이언트
    }
    if (ip == null || ip.isEmpty() || "unknown".equalsIgnoreCase(ip)) {
        ip = request.getHeader("Proxy-Client-IP");
    }
    ...생략
    if (ip == null || ip.isEmpty() || "unknown".equalsIgnoreCase(ip)) {
        ip = request.getRemoteAddr();
    }
    return ip;
}
```

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft"><tbody><tr><td style="width: 18.2558%;"><span style="font-family: 'Noto Serif KR';"><b>항목</b></span></td><td style="width: 81.7442%;"><span style="font-family: 'Noto Serif KR';"><b>설명</b></span></td></tr><tr><td style="width: 18.2558%;"><span style="font-family: 'Noto Serif KR';"><b>개발환경</b></span></td><td style="width: 81.7442%;"><span style="font-family: 'Noto Serif KR';"><b>개발 서버에선 getRemoteAddr()로도 충분히 동작</b></span></td></tr><tr><td style="width: 18.2558%;"><span style="font-family: 'Noto Serif KR';"><b>운영환경</b></span></td><td style="width: 81.7442%;"><span style="font-family: 'Noto Serif KR';"><b>Nginx, AWS ALB, WAF 등 프록시 거칠 경우 getRemoteAddr()은 프록시 IP만 반환</b></span></td></tr><tr><td style="width: 18.2558%;"><span style="font-family: 'Noto Serif KR';"><b>보안고려</b></span></td><td style="width: 81.7442%;"><span style="font-family: 'Noto Serif KR';"><b>X-Forwarded-For&nbsp;헤더는&nbsp;조작&nbsp;가능성이&nbsp;있으므로,&nbsp;신뢰할&nbsp;수&nbsp;있는&nbsp;프록시만&nbsp;사용할&nbsp;때&nbsp;검증&nbsp;필요</b></span></td></tr><tr><td style="width: 18.2558%;"><span style="font-family: 'Noto Serif KR';"><b>결론</b></span></td><td style="width: 81.7442%;"><span style="font-family: 'Noto Serif KR';"><b>보통은 getUserIp() 방식이 더 정확하고 실무 친화적이며, 프록시 환경에 대비한 코드로 추천됨</b></span></td></tr></tbody></table>

<table style="border-collapse: collapse; width: 100%; height: 185px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 21px;"><td style="width: 26.8217%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>헤더 이름</b></span></td><td style="width: 39.8449%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>설명</b></span></td><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>주로&nbsp;사용되는&nbsp;환경</b></span></td></tr><tr style="height: 21px;"><td style="width: 26.8217%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>X-Forwarded-For</b></span></td><td style="width: 39.8449%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>가장&nbsp;일반적인&nbsp;클라이언트&nbsp;IP&nbsp;전파&nbsp;헤더</b></span></td><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>대부분의&nbsp;프록시,&nbsp;로드밸런서&nbsp;(Nginx&nbsp;등)</b></span></td></tr><tr style="height: 21px;"><td style="width: 26.8217%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>Proxy-Client-IP</b></span></td><td style="width: 39.8449%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>일부&nbsp;Apache&nbsp;프록시&nbsp;또는&nbsp;Weblogic</b></span></td><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>오래된&nbsp;환경</b></span></td></tr><tr style="height: 21px;"><td style="width: 26.8217%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>WL-Proxy-Client-IP</b></span></td><td style="width: 39.8449%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>WebLogic&nbsp;전용&nbsp;헤더</b></span></td><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>Oracle&nbsp;WebLogic</b></span></td></tr><tr style="height: 21px;"><td style="width: 26.8217%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>HTTP_CLIENT_IP</b></span></td><td style="width: 39.8449%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>일부&nbsp;프록시&nbsp;환경</b></span></td><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>잘&nbsp;쓰이진&nbsp;않음</b></span></td></tr><tr style="height: 21px;"><td style="width: 26.8217%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>HTTP_X_FORWARDED_FOR</b></span></td><td style="width: 39.8449%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>X-Forwarded-For의&nbsp;다른&nbsp;표현</b></span></td><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>IIS&nbsp;등에서&nbsp;사용</b></span></td></tr><tr style="height: 21px;"><td style="width: 26.8217%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>X-Real-IP,&nbsp;X-RealIP</b></span></td><td style="width: 39.8449%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>Nginx&nbsp;등에서&nbsp;클라이언트&nbsp;IP를&nbsp;명확하게&nbsp;전달</b></span></td><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>Nginx에서&nbsp;종종&nbsp;사용</b></span></td></tr><tr style="height: 21px;"><td style="width: 26.8217%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>REMOTE_ADDR&nbsp;(헤더)</b></span></td><td style="width: 39.8449%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>표준&nbsp;헤더는&nbsp;아니며,&nbsp;일부&nbsp;프록시에서&nbsp;세팅</b></span></td><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>신뢰도&nbsp;낮음</b></span></td></tr><tr style="height: 17px;"><td style="width: 26.8217%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>getRemoteAddr()</b></span></td><td style="width: 39.8449%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>WAS와&nbsp;직접&nbsp;연결된&nbsp;최종&nbsp;요청자의&nbsp;IP&nbsp;</b></span><br><span style="font-family: 'Noto Serif KR';"><b>(프록시일&nbsp;수&nbsp;있음)</b></span></td><td style="width: 33.3333%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>마지막&nbsp;보루</b></span></td></tr></tbody></table>
