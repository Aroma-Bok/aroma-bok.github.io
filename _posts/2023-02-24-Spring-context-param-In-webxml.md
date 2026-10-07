---
title: "[Spring] context-param In web.xml"
date: 2023-02-24 12:31:20 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["context-param"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-context-param-In-webxml"
---
**※ STS에서 기본적으로 제공해 주는 설정 파일 외에, 사용자가 직접 컨트롤하는 XML파일을 지정해 주는 역할**

**※ root-context : 여기에 등록되는 bean들은 모든 context에서 사용된다.  
**※** servlet-context : 여기에 등록되는 bean들은 servlet-context 에서만 사용된다.  
**※** bean이 겹치는 경우에는 servlet-context에 있는 bean을 사용  
**
