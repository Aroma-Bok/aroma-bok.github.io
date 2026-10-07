---
title: "[Server] Apache Tomcat과 JBoss 개념 및 차이점 정리"
date: 2024-12-24 11:31:02 +0900
categories: ["Solution & Tools", "Server"]
tags: ["jboss", "jboss tomcat 차이", "jboss vs tomcat", "tomcat", "제이보스 톰캣", "제이보스 톰캣 차이"]
tistory_url: "https://aroma-bok.tistory.com/entry/Server-Apache-Tomcat%EA%B3%BC-JBoss-%EA%B0%9C%EB%85%90-%EB%B0%8F-%EC%B0%A8%EC%9D%B4%EC%A0%90-%EC%A0%95%EB%A6%AC"
---
**이번 프로젝트에서 JBoss를 사용하게 되어서 Apache Tomcat과 JBoss 개념 및 차이점을 정리하였다.** 

* * *

![](/assets/img/posts/286/1.png)

### **Apache Tomcat**

-   **웹 애플리케이션을 개발하기 위해서 필요한 웹 컨테이너를 구현한 것이 바로 Apache Tomcat**
-   **Java EE표준에 포함되어 있는 JSP, Servlet 등과 같은 웹 애플리케이션 개발을 위한 표준을 구현한 웹 컨테이너**

> **Java EE 표준에는 다양한 컨테이너가 정의되어 있는데 대표적인 컨테이너가 웹 컨테이너와 EJB 컨테이너**  
>   
> **웹 컨테이너 (Web Container)**  
> **▶ Servlet, JSP, WebSocket 등의 웹 애플리케이션 컴포넌트를 실행하고 관리하는 환경 제공**  
> **▶ 클라이언트에서 오는 Http 요청을 처리하며, 동적인 웹 컨텐츠 생성**  
> **▶ ex : Apache Tomcat, Jetty**  
>   
> **EJB 컨테이너 ( Enterprise JavaBeans Container) == WAS**  
> **▶ Enterprise JavaBeans를 실행하고 관리하는 환경 제공**  
> **▶ 주로 비즈니스 로직, 트랜잭션, 보안 및 분산처리 지원**  
> **▶ JBoss/WildFly, WebLogic Sever**   
>   
> **EJB 컨테이너는 WAS의 일부로, 비즈니스 로직과 관련된 역할을 수행**

### **Apache Tomcat으로만 서버를 구축가능 하지 않나?**

▶ **Tomcat은 기술적으로는 응용 프로그램 서버가 아니다. **Web Server와 Web Container에 가깝다.****

▶ **보통** **JBoss와 같은 Java EE 표준을 모두 구현한 서버를 사용해야 한다. 그러나 단순한 웹 애플리케이션을 구현하는 경우에는** **Apache Tomcat을 사용할 수 있다.** 

* * *

### **JBoss / WildFly**

-   **Red Hat에서 관리 및 개발**
-   **Java EE / Jakarta EE 표준을 준수하는 애플리케이션 서버**
-   **대규모 엔터프라이즈 애플리케이션 개발과 배포를 지원**
-   **무료로 사용할 수 있는 오픈소스 프로젝트**

* * *

### **Apache Tomcat vs JBoss**

**JBoss와 Tomcat은 모두 Java Servlet Application 서버지만 JBoss는 훨씬 더 기능이 많다.**

**이 둘의 가장 큰 차이점은 JBoss가 EJB 및 엔터프라이즈 Java 응용 프로그램에서 작업하는 개발자에게 유용한 기타 기술을 포함하여 완전한 Java Enterprise Edition(JEE) 스택을 제공한다.**

**Tomcat은 훨씬 더 제한적이다. JBoss가 Servlet Container와 Web Server를 포함하는 JEE 스택인 반면 Tomcat은 대부분 Servlet Container와 Web Server이다.**

**JBoss에는 기본적으로 Tomcat이 내장되어 있으므로 Tomcat을 사용했었던 사용자는 손쉽게 JBoss로 웹 애플리케이션을 이전가능하다.**
