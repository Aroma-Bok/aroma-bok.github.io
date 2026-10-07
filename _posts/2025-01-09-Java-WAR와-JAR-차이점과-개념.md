---
title: "[Java] WAR와 JAR 차이점과 개념"
date: 2025-01-09 10:34:10 +0900
categories: ["Language", "Java"]
tags: ["jar 란", "jar 배포", "war jar", "war jar 배포 차이", "war jar 사용 시기", "war jar 차이", "war 란", "war 배포"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-WAR%EC%99%80-JAR-%EC%B0%A8%EC%9D%B4%EC%A0%90%EA%B3%BC-%EA%B0%9C%EB%85%90"
---
![](/assets/img/posts/300/1.jpg)

#### **WAR vs JAR**

-   **War와 Jar파일은 모두 Java에서 사용되는 압축 파일 형식으로, 애플리케이션을 배포하고 실행할 때 사용**
-   **용도와 내부 구조에서 차이가 있다.**

* * *

#### **JAR (Java Archive)**

-   **Java 프로그램에서 클래스 파일 및 리소스 파일을 압축한 파일 형식**

#### **용도**

-   **일반 Java 애플리케이션 배포**
-   **독립 실행형 프로그램, 라이브러리, 유틸리티 제공**

#### **구조**

-   **META-INF/디렉터리(필수)**
-   **MAINFEST.MF : Jar 파일의 메타데이터를 포함**
-   **. class 파일, 리소스파일(예: 이미지, 설정파일)**

#### **실행**

-   **실행가능한 Jar 파일은 MAINFEST.MF 파일에 Main-Class 항목을 정의해야 함**
-   **실행 예 : java -jar mapp.jar**

**특징**

-   **독립 애플리케이션 또는 라이브러리 파일로 사용**
-   **일반적으로 클라이언트 측 애플리케이션 배포 시 사용**

* * *

#### **WAR (Web Application Archive)**

-   **Java 웹 애플리케이션 배포를 위한 파일 형식**

#### **용도**

-   **Java 웹 애플리케이션 배포 (Servlet, JSP 기반)**
-   **서버 측 웹 애플리케이션 실행**

#### **구조**

-   **WEB-INF/디렉터리(필수)  
    ****web.xml : 웹 애플리케이션의 배포 설명자 파일  
    ****classes/: 컴파일된 클래스 파일  
    ****lib/    : 애플리케이션에서 사용하는 JAR 라이브러리  
    ****JSP 파일, 정적 리소스(HTML, CSS, JS 등)**

#### **실행**

-   **WAR 파일은 독립 실행이 불가능 하며, 서버(예: Apache Tomcat, JBoss, WildFly)에 배포해야 함**
-   **실행 예  
    ****1\. myapp.war 파일을 서버의 webapps 디렉토리에 복사  
    ****2\. 서버가 실행 중이라면 자동으로 애플리케이션이 배포됨**

#### **특징**

-   **웹 애플리케이션용**
-   **Servlet 컨테이너나 애플리케이션 서버에서 실행**

* * *

#### **주요 차이점**

<table style="border-collapse: collapse; width: 100%; height: 105px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 21px;"><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>항목</b></span></td><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #006dd7;"><b>WAR 배포</b></span></td><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>JAR 배포</b></span></td></tr><tr style="height: 21px;"><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>배포 방식</b></span></td><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #006dd7;"><b>외부 WAS에 의해 관리</b></span></td><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>자체 실행 파일로 독립실행</b></span></td></tr><tr style="height: 21px;"><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>배포 디렉토리</b></span></td><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>WAS의 wabapps/ 또는 deployments/</b></span></td><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>자유롭게 지정 가능 (/opt/app/)</b></span></td></tr><tr style="height: 21px;"><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>실행 방식</b></span></td><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>WAS 서버 시작 시 자동 배포</b></span></td><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>직접 실행 (java -jar)</b></span></td></tr><tr style="height: 21px;"><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>추가 소프트웨어</b></span></td><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #006dd7;"><b>WAS 설치 필요</b></span></td><td style="width: 33.3333%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>추가 소프트웨어 불필요</b></span></td></tr></tbody></table>

#### **사용 시점**

-   **JAR  
    독립 실행형 Java 애플리케이션, 유틸리티, 라이브러리 제공 시 사용**
-   **WAR  
    Java 기반 웹 애플리케이션 개발 및 배포 시 사용**

#### **예시**

-   **JAR  
    ****Spring Boot 애플리케이션(단일 JAR 파일로 실행 가능)  
    ****독립 실행형 Java 프로그램**
-   **WAR  
    ****JSP/Servlet 기반의 전통적인 Java 웹 애플리케이션  
    ****RESTful 웹 서비스 배포**

#### **결론  
▷ JAR은 일반 애플리케이션용, WAR은 웹 애플리케이션 배포용** 

* * *

### **JA****R 파일을 사용하는 웹 개발 증가의 이유**

#### **1\. Spring Boot의 등장**

-   **Spring Boot는 내장 웹 서버(예: Tomcat, Jetty, Undertow)를 포함한 독립 실행형 애플리케이션을  
    ****쉽게 만들 수 있도록 설계**
-   **WAR 파일을 따로 생성하지 않고, 실행 가능한 JAR 파일로 모든 것을 포함하여 배포 가능**
-   **즉, 웹 애플리케이션 서버와 애플리케이션을 하나의 JAR 파일로 묶는 방식을 제공**

#### **2\. 컨테이너화 클라우드 환경**

-   **현대 개발에서는 애플리케이션을 Docker 컨테이너에 배포하거나 클라우드 서비스에서 실행하는 경우가 많음**
-   **이 경우 내장 서버를 포함한 JAR 파일로 패키징하여 배포하면 훨씬 간단하고 관리가 용이**
-   **클라우드 환경에서는 "애플리케이션 서버"를 따로 관리하지 않고, 애플리케이션 자체를 컨테이너 화하여 실행하는 방식이 일반적**

#### **3\. DevOps 및 CI/CD의 보편화**

-   **JAR 파일은 자체젝으로 실행 가능한 파일이기 때문에 빌드-테스트-배포의 자동화 파이프라인에서 활용하기 용이**
-   **WAR 파일은 별도의 애플리케이션 서버에 배포해야 하므로 추가적인 설정과 관리가 필요**

#### **4\. 마이크로 서비스 아키텍처**

-   **마이크로서비스에서는 독립적으로 실행 가능한 애플리케이션을 많이 사용하며, JAR 파일로 패키징 하는 방식이 적합**
-   **WAR 파일은 전통적인 모놀리식 아키텍처에 더 적합한 방식**
