---
title: "[Spring Framework] Maven으로 JRE System Library JavaSE-1.8로 변경하기"
date: 2022-05-16 22:17:28 +0900
categories: ["Framework & Library", "Spring Framework"]
tags: ["jre 1.8로 변경", "maven으로 jre system library javase-1.8로 변경하기"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Framework-Maven%EC%9C%BC%EB%A1%9C-JRE-System-Library-JavaSE-18%EB%A1%9C-%EB%B3%80%EA%B2%BD%ED%95%98%EA%B8%B0"
---
## **Maven으로 JRE System Library JavaSE-1.8로 변경하기**

* * *

#### **1\. pom.xml에 속성 추가**

```java
  	<maven.compiler.source>1.8</maven.compiler.source>
  	<maven.compiler.target>1.8</maven.compiler.target>
```

#### **2\. 해당 프로젝트 Maven 업데이트**
