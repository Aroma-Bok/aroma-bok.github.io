---
title: "[SpringBoot] 클래스패스(ClassPath)와 파일패스(FilePath) 차이, 그리고 실제 프로젝트 적용 사례 정리"
date: 2025-12-10 18:00:41 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["classpath vs filepath", "자바 라이브러리 import 경로", "클래스패스란?", "클래스패스와 파일패스 차이", "파일패스란?"]
tistory_url: "https://aroma-bok.tistory.com/entry/SpringBoot-%ED%81%B4%EB%9E%98%EC%8A%A4%ED%8C%A8%EC%8A%A4ClassPath%EC%99%80-%ED%8C%8C%EC%9D%BC%ED%8C%A8%EC%8A%A4FilePath-%EC%B0%A8%EC%9D%B4-%EA%B7%B8%EB%A6%AC%EA%B3%A0-%EC%8B%A4%EC%A0%9C-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EC%A0%81%EC%9A%A9-%EC%82%AC%EB%A1%80-%EC%A0%95%EB%A6%AC"
---
**프로젝트를 진행하다 보면 설정 파일을 어디에 두고 어떻게 읽어야 할지 헷갈릴 때가 많다.**

**특히 "클래스패스(ClassPath) 기반과 파일패스(FilePath) 기반의 차이"를 명확하게 이해하지 못하면 배포 시 파일 경로 문제로 오류가 자주 발생하게 된다.**

**이번 글에는 직접 겪은 문제를 해결하는 과정을 기반으로 클래스패스(ClassPath) 기반과 파일패스(FilePath)의 차이를 정리했다.**

* * *

#### **1\. 클래스패스(ClassPath) 기반?**

-   **Java 애플리케이션이 실행될 때, 클래스와 리소스를 찾기 위해 미리 설정된 경로**
-   **SpringBoot 기준으로는 다음 경로가 클래스패스에 해당**  
    **\- src/main/resources\*\* → (빌드 후) target/classes/\*\***
-   **즉, resources 폴더 안에 넣어두기만 하면 빌드할 때 자동으로 JAR/WAR 내부에 포함**

```java
ClassLoader cl = Thread.currentThread().getContextClassLoader();
URL url = cl.getResource("app/config/app.properties");
```

#### **특징**

1.  **JAR/WAR 내부에 포함됨**
2.  **서버에 추가 파일을 따로 올릴 필요 없음**
3.  **배포할 때 파일 경로 차이로 인해 깨질 위험 없음**
4.  **운영체제(OS)에 의존하지 않음**
5.  **프로젝트에 포함해 두면 언제든 ClassPath에서 읽을 수 있음**

* * *

#### **2\. 파일패스(FilePath) 기반?**

-   **운영체제가 가진 실제 디렉토리 위치를 직접 경로로 설정하는 방식**

```java
D:/kiehl/polaris/MLE2E/.../jcaos.lic
/opt/config/app/app.properties
```

#### **특징**

1.  **운영서버의 실제 파일 위치를 기반으로 읽음**
2.  **파일이 존재하지 않으면 반드시 오류 발생**
3.  **Windows / Linux 경로 차이로 문제가 생기기 쉬움**
4.  **서버 배포 시 반드시 해당 파일을 직접 올려줘야 함**
5.  **OS에 따라 절대경로가 달라지기 때문에 배포 환경에서 문제를 유발하기 쉬움**

* * *

#### **3\. 실제 문제 상황**

**처음에는 다음과 같이 문자열 경로만 넘겨서 설정 파일을 로딩하려고 했다.**

```java
E2EConfigLoader.getInstance().loadConfigDir("app/config");
```

**문제는, 해당 라이브러리의 loadConfigDir() 메서드가 단순 ClassPath 상대경로를 받지 않고,**

**실제 파일 시스템상의 디렉토리 경로(절대경로)를 요구한다.**

-   **app/config → 이것은 ClassPath 내부 경로 문자열**
-   **라이브러리가 요구하는 값 → 실제 OS 파일 경로**

**따라서 문자열 "app/config"로는 라이브러리가 실제 디렉토리를 찾지 못해서 파일 존재 오류가 계속 발생.**

#### **4\. 해결방법**

**그래서 아래처럼 ClassPath 내부 디렉토리를 먼저 URL로 찾고,** 

**그걸 다시 OS 절대경로로 변환한 뒤 loadConfigDir()에 넘기니 해결했다.**

```java
URL url = getClass().getClassLoader().getResource("app/config");
String dirPath = Paths.get(url.toURI()).toString();
E2EConfigLoader.getInstance().loadConfigDir(dirPath);
```

**이 과정에서 실제 처리 흐름**

1.  **ClassLoader가 ClassPath 안의 디렉토리를 찾음**
2.  **URL 형태로 반환 (예: file:/D/.../tartget/classes/app/config)**
3.  **Paths.get(url.toURI()) 로 실제 OS 경로로 변환**
4.  **라이브러리가 요구하는 "실제 파일 경로" 형태로 loadConfigDir 호출**

* * *

#### **참고**

#### **loadConfigDir(String confDir) 내부 동작 분석 (왜 ClassPath가 안 되었는가?)**

#### **1\. confDir 문자열을 그대로 사용**

```java
if (!confDir.endsWith("/") && !confDir.endsWith("\\")) {
    // do nothing
} else {
    // 뒤에 '/' 또는 '\' 있으면 잘라냄
    confDir = confDir.substring(0, confDir.length() - 1);
}
```

**전달된 confDir 문자열은 오직 문자열 기준으로만 검사**

**즉, ClassPath resource인지, FilePath인지 아무 판단 안 하고 그냥 문자열로만 처리**

#### **2\. 문자열 이어 붙여서 실제 파일 경로로 만듦**

```java
configFilePath = confDir + "/app.properties";
licFilePath = confDir + "/jcaos.lic";
```

**받은 confDir이 "app/config"이라면,**

```java
"app/config" + "/app.properties"
→ app/config/app.properties
```

**해당 라이브러리는 문자열이 "파일패스(FilePath)"라고 가정하고 있다.**

**ClassPath 내부 파일을 읽는 로직은 전혀 없다.**

-   **classpath:app/config/...**
-   **target/classes/app/config/...**

**이러한 경로 둘 다 처리할 수 없다. 오직 실제 파일 경로만 처리 가능.**

* * *

#### **참고**

#### **대표적으로 자누 쓰는 클래스패스(ClassPath) 기반 메서드**

#### **1\. @Value("classpath:...") (Spring Framework )**

```java
@Value("classpath:magicline/config/app.properties")
private Resource resource;
```

#### **2\. CalssPathResource (Spring Framework)**

```java
Resource resource = new ClassPathResource("app/config/app.properties");
```

#### **3\. ResourceLoader (Spring Framework)**

```java
@Autowired
ResourceLoader resourceLoader;

Resource resource = resourceLoader.getResource("classpath:app/config/app.properties");
```

#### **4\. ClassLoader#getResource() (Java)**

```java
URL url = getClass().getClassLoader().getResource("app/config");
```

#### **5\. Class#getResourceAsStream (Java)**

```java
InputStream is = getClass().getResourceAsStream("/app/config/app.properties");
```

#### **6\. 자동 로딩 리소스들 (Spring Boot)**

-   **src/main/resources/**
-   **static/**
-   **templates/**
-   **application.properties / yml**
