---
title: "[JavaScript] Window.location객체 (+Document객체)"
date: 2021-10-25 23:14:17 +0900
categories: ["Language", "JavaScript"]
tags: ["document객체", "javascript", "jquery", "location객체", "window객체", "자바스크립트"]
tistory_url: "https://aroma-bok.tistory.com/entry/JavaScript-Windowlocation%EA%B0%9D%EC%B2%B4-Document%EA%B0%9D%EC%B2%B4"
---
웹을 개발하는 개발자라면 누구나 많이 접해볼 window 객체에 대해서 조금 더 정리가 필요해서 공부를 하였다.

오늘은 window.location 때문에 이 객체가 뭐 였지..? 라는 생각으로 공부를 시작하게 되었고, 다시한번 머리속에 정리를 하였다.

* * *

#### **Window 객체 :** 

**브라우저를 제어하기 위한 객체 (JavaScript의 최상위 객체)**

자바스크립트의 모든 객체는 window 객체의 하위 객체로서, 내장객체에 접근 시, window를 붙여줘야한다.

ex) window.document.getElementById("ID속성값");

하지만, 생략하고 사용 가능하다.

ex) document.getElementById("ID속성값");

* * *

#### location 객체 ( = window.location)

-   웹 브라우저의 주소 표시줄(=주소창) 을 제어
-   주소 표시줄의 주소 혹은 그 일부를 조회 및 변경 가능
-   주소를 변경하게 되면, 웹 브라우저는 페이지를 이동 시킴

location 객체의 속성 (location.속성)

-   href - URL 주소
-   host - 호스트 이름과 포트
-   hostname - 호스트 컴퓨터 이름
-   hash - 앵커 이름
-   pathname - 디렉토리 이하 경로
-   port - 포트번호
-   protocol - 프로토콜 종류
-   search - URL 조회 부분

* * *

#### **Document객체:**

**HTML 문서를 제어하는 내장 객체, HTML 태그 요소를 id값에 의하여 객체 형태로 가져오는 기능 제공**

**ex) document.getElementById("Id속성값");**

**CSS속성에 대응되는 변수들이 내장되어 있는 style 객체를 사용하여 CSS를 제어할 수도 있다.**

**\=> document.getElementById("Id속성값").style.CSS대응속성 = "CSS적용값";**

**하지만, 이렇게 사용하면 CSS의 속성 이름들이 조금씩 차이가 있어서 별도로 기억해야 하는 상황이 생긴다.**

**그래서  CSS의 속성 이름을 그대로 사용하기 위해서 **jQuery를 사용하는 것이라고 배웠다.****
