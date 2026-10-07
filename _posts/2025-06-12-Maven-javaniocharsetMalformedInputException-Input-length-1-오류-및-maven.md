---
title: "[Maven] java.nio.charset.MalformedInputException: Input length = 1 오류 및 maven-resources-plugin"
date: 2025-06-12 15:27:50 +0900
categories: ["ETC", "IT Knowledge"]
tags: ["java.nio.charset.malformedinputexception: input length = 1", "maven-resources-plugin", "메이븐 install 오류", "메이븐 오류"]
tistory_url: "https://aroma-bok.tistory.com/entry/Maven-javaniocharsetMalformedInputException-Input-length-1-%EC%98%A4%EB%A5%98-%EB%B0%8F-maven-resources-plugin"
---
**프로젝트 진행하면서 개발하면서 Maven clean & install을 하는 상황이 있다.**

**이 상황에서 install을 하면서 "java.nio.charset.MalformedInputException: Input length = 1" 오류가**

**발생해서  정리한 내용**

* * *

**maven-resources-plugin 이란?**

-   **Maven의 리소스(예:. properties,. xml,. txt 등)를 복사하고 필터링하는 데 사용되는 플러그인**
-   **주로 src/main/resources, src/test/resources의 파일들을 target 디렉터리로 복사**

**문제의 원인**

-   **Maven이 기본 내장된 구버전의 maven-resources-plugin을 사용 (예: 3.2.0 이하)**
-   **해당 버전에서는 다음과 같은 버그가 존재**  
    **1) UTF-8 인코딩이 올바르게 처리되지 않음  
    2) 특정 OS환경에서 파일이 깨지거나 누락되는 형상  
    3) 특수 문자, 한글 등이 포함된 파일에서 인코딩 오류 발생**

**✅ 해결 방법**

-   **pom.xml에 명시적으로 최신 버전 추가**
-   **최신 버전에서는 인코딩 처리, 파일 필터링 로직 등 여러 문제가 해결됨**
-   **특히 UTF-8 인코딩 지원이 안정적으로 동작함**

```java
<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-resources-plugin</artifactId>
	<version>3.3.0</version>
</plugin>
```

**✅ 정리**

<table style="border-collapse: collapse; width: 100%; height: 105px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 21px;"><td style="width: 12.4419%; height: 21px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">항목<span>&nbsp;</span></span></b></td><td style="width: 87.5581%; height: 21px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">내용</span></b></td></tr><tr style="height: 21px;"><td style="width: 12.4419%; height: 21px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">문제<span>&nbsp;</span></span></b></td><td style="width: 87.5581%; height: 21px; text-align: center;"><span><b><span style="font-family: 'Noto Serif KR';">Maven 빌드시 리소스 처리 오류 발생 (파일 깨짐, 인코딩 문제 등)</span></b></span></td></tr><tr style="height: 21px;"><td style="width: 12.4419%; height: 21px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">원인</span></b></td><td style="width: 87.5581%; height: 21px; text-align: center;"><span><b><span style="font-family: 'Noto Serif KR';">maven-resources-plugin의 구버전 사용 (3.2.0 이하)</span></b></span></td></tr><tr style="height: 21px;"><td style="width: 12.4419%; height: 21px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">해결</span></b></td><td style="width: 87.5581%; height: 21px; text-align: center;"><span><b><span style="font-family: 'Noto Serif KR';">pom.xml에 명시적으로 3.3.0 이상 버전 설정</span></b></span></td></tr><tr style="height: 21px;"><td style="width: 12.4419%; height: 21px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">효과</span></b></td><td style="width: 87.5581%; height: 21px; text-align: center;"><b><span style="font-family: 'Noto Serif KR';">인코딩&nbsp;문제&nbsp;해결,&nbsp;리소스&nbsp;복사&nbsp;안정성&nbsp;향상,&nbsp;깨짐&nbsp;현상&nbsp;사라짐</span></b></td></tr></tbody></table>
