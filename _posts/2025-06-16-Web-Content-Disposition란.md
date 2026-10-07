---
title: "[Web] Content-Disposition란?"
date: 2025-06-16 16:43:56 +0900
categories: ["Solution & Tools", "Web"]
tags: ["access-control-expose-headers", "content-disposition", "파일 다운로드 관련 content-disposition", "파일다운로드시 헤더 적용"]
tistory_url: "https://aroma-bok.tistory.com/entry/Web-Content-Disposition%EB%9E%80"
---
**Content-Disposition**

-   **Content-Disposition은 HTTP 응답 헤더 중 하나**
-   **서버가 브라우저(클라이언트)에게** **"이 데이터를 화면에 보여줄지, 아니면 파일로 다운로드하게 할지"지시하는 역할**

**어떻게 동작?**

-   **Content-Disposition: inline**  
    **브라우저가 내용을 바로 화면에 표시합니다. (예: 이미지, PDF 등 웹에서 바로 볼 수 있는 파일）**
-   **Content-Disposition: attachment; filename="파일명.jpg"**  
    **브라우저가 내용을 파일로 다운로드하게 만듭니다. filename 파라미터로 저장될 파일명을 지정할 수 있다.**

<table style="border-collapse: collapse; width: 99.6513%;" border="1" data-ke-align="alignLeft"><tbody><tr><td style="width: 12.2868%;"><span style="font-family: 'Noto Serif KR';"><b>방식</b></span></td><td style="width: 37.6022%;"><span style="font-family: 'Noto Serif KR';"><b>설명</b></span></td><td style="width: 49.7916%;"><span style="font-family: 'Noto Serif KR';"><b>예시 코드</b></span></td></tr><tr><td style="width: 12.2868%;"><span style="font-family: 'Noto Serif KR';"><b>inline</b></span></td><td style="width: 37.6022%;"><span style="font-family: 'Noto Serif KR';"><b><span style="color: oklch(0.304 0.04 213.681); text-align: start;">브라우저가 내용을&nbsp;</span>웹페이지 안에서 바로 표시<span style="color: oklch(0.304 0.04 213.681); text-align: start;">.<br>(이미지, PDF 등)</span></b></span></td><td style="width: 49.7916%;"><span style="font-family: 'Noto Serif KR';"><b><span style="color: oklch(0.304 0.04 213.681); text-align: start;">Content-Disposition: inline</span></b></span></td></tr><tr><td style="width: 12.2868%;"><span style="font-family: 'Noto Serif KR';"><b><span style="color: oklch(0.304 0.04 213.681); text-align: start;">attachment</span></b></span></td><td style="width: 37.6022%;"><span style="font-family: 'Noto Serif KR';"><b><span style="color: oklch(0.304 0.04 213.681); text-align: start;">브라우저가 내용을&nbsp;</span>파일로 다운로드<span style="color: oklch(0.304 0.04 213.681); text-align: start;"><br>(대부분의 파일 다운로드에 사용)</span></b></span></td><td style="width: 49.7916%;"><span style="font-family: 'Noto Serif KR';"><b><span style="color: oklch(0.304 0.04 213.681); text-align: start;">Content-Disposition: attachment; filename="파일명.jpg"</span></b></span></td></tr></tbody></table>

**왜 필요?**

-   **서버가 파일(문서, 이미지, 엑셀 등)을 내려줄 때 사용자가 다운로드 받도록 만들고 싶을 때 꼭 필요**
-   **Content-Disposition: attachment; filename="report.xlsx" 이렇게 설정하면 브라우저는 파일을 저장하라는 창을 띄운다.**

**한글/특수문자 파일명은?**

-   **파일명이 한글 등 특수문자일 때는**   
    **Content-Disposition: attachment; filename\*=UTF-8''%ED%95%9C%EA%B8%80.xlsx 처럼  
    UTF-8로 인코딩해서 보내면 브라우저에서 파일명이 깨지지 않는다.**

**요약**

-   **Content-Disposition:서버가 브라우저에게 "이 파일을 어떻게 처리할지" 알려주는 헤더  
    ****(화면에 바로 보여줄지, 다운로드하게 할지)**
-   **filename:다운로드 시 저장될 파일명을 지정**
-   **filename\*:한글/특수문자 파일명을 위해 UTF-8 인코딩 사용**
-   **Content-Disposition 헤더를 사용하면 서버는 클라이언트에게 특정 데이터를 화면에 표시하는 대신 파일로 다운로드하도록 지시할 수 있으며, 원하는 파일 이름을 제안할 수도 있습니다.**

**++**

**추가적으로 Access-Control-Expose-Headers가 필요한 경우가 있다.**

**이 코드는 CORS(교차 출처 리소스 공유) 환경에서, 브라우저의 자바스크립트가 서버 응답의 Content-Disposition 헤더 값에 접근할 수 있도록 허용해 주는 역할을 한다.**

**브라우저는 보안상, CORS 요청(다른 도메인 간 요청)에서 일부 기본 헤더(Cache-Control, Content-Type 등)만 자바스크립트에서 읽을 수 있게 허용해 준다.**

**Content-Disposition과 같은 추가적인 헤더는 기본적으로 자바스크립트가 읽을 수 없다.**

**서버에서 아래처럼 응답헤더를 추가하면,**

```java
Access-Control-Expose-Headers: Content-Disposition
```

**프론트 JS 코드에서 아래처럼 헤더 값을 읽을 수 있다.**

```java
response.headers.get('Content-Disposition')
```

**즉, Access-Control-Expose-Headers는 프론트(JS)에서 읽어도 된다고 서버가 명시적으로 허용하는 역할이다.**
