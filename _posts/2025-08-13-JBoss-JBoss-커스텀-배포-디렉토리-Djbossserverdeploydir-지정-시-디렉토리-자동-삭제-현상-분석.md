---
title: "[JBoss] JBoss 커스텀 배포 디렉토리(-Djboss.server.deploy.dir) 지정 시 디렉토리 자동 삭제 현상 분석"
date: 2025-08-13 10:18:37 +0900
categories: ["Solution & Tools", "Server"]
tags: ["jboss 디렉토리 자동 삭제", "jboss 배포 디렉토리", "jboss 실행 명령어", "제이보스 실행 명령어"]
tistory_url: "https://aroma-bok.tistory.com/entry/JBoss-JBoss-%EC%BB%A4%EC%8A%A4%ED%85%80-%EB%B0%B0%ED%8F%AC-%EB%94%94%EB%A0%89%ED%86%A0%EB%A6%AC-Djbossserverdeploydir-%EC%A7%80%EC%A0%95-%EC%8B%9C-%EB%94%94%EB%A0%89%ED%86%A0%EB%A6%AC-%EC%9E%90%EB%8F%99-%EC%82%AD%EC%A0%9C-%ED%98%84%EC%83%81-%EB%B6%84%EC%84%9D"
---
**1\. 배경**

**JBoss(EAP, WildFly) 서버를 실행할 때, 보통은 다음과 같이 기본 배포 디렉토리를 사용한다.**

```java
nohup sh ./bin/standalone.sh -Dspring.profiles.active=dev &
```

**이 경우 JBoss는 내부 기본 디렉토리(예: standlone/deployments) 또는 관련 경로를 사용하여 배포를 관리하며,** 

**운영 중에도 디렉토리가 임의로 삭제되는 일은 거의 없다.**

**그런데 아래처럼 \-Djboss.server.deploy.dir 옵션으로 커스텀 배포 디렉토리를 지정하면 문제가 발생할 수 있다.**

```java
nohup ./bin/standalone.sh -Dspring.profiles.active=dev -Djboss.server.deploy.dir=커스텀경로
```

* * *

**2\. 문제현상**

-   **커스텀 배포 디렉토리를 지정한 상태에서 JBoss를 재시작하거나, 배포 작업이 발생하면**  
    **사용자 조작 없이 해당 디렉토리 혹은 그 안의 파일이 자동으로 삭제됨.**
-   **특히 재배포, undeploy, 서버 재시작 시 디렉토리 자체가 recreate 되거나 내부 파일이 모두 지워짐.**

* * *

**3\. 원인분석**

**3.1 JBoss의 배포 스캐너(Deployment Scanner) 동작**

-   **JBoss는 배포 디렉토리를 배포 감시 경로로 인식하고, 주기적으로 상태를 체크한다.**
-   **디렉토리 내 파일.마커(.deployed, .undeployed, .failed 등) 변경을 감지해 배포/언디플로이 동작을 실행**

**3.2 커스텀 경로 지정 시 동작 변화**

-   **기본 경로(standalone 폴더 내부)는 시스템이 안전하게 유지 및 관리**
-   **\-Djboss.server.deploy.dir 로 지정한 경로는 JBoss가 전체를 "내부 관리 대상"으로 등록**
-   **관리 대상이 된 디렉토리는 다음 이벤트에서 자동 청소(Cleanup) 수행**  
    **1\. 서버 재시작**  
    **2\. undeploy 또는 redeploy 명령**  
    **3\. 실패한 배포 롤백**
-   **이 과정에서 기존 배포 디렉토리 내용이 통째로 삭제될 수 있음**

**3.3 설계 의도**

-   **JBoss는 배포 디렉토리를 "배포 컨테츠 전용"으로 쓰고, 외부 파일이 섞이지 않도록 보장하려 함**
-   **커스텀 경로도 동일하게 취급 → 라이프사이클(생성/삭제)까지 자동 관리**

* * *

**4\. 해결 방안**

1.  **커스텀 배포 디렉토리 사용 지양**  
    ****→**  특별한 이유가 없다면 기본 배포 경로 사용이 가장 안전**
2.  **불가피하게 사용할 경우**  
    ****→**  해당 경로를 JBoss 전용으로 할당해 다른 서비스/데이터와 공유하지 않지**  
    ****→**  deployment-scanner 서브시스템 설정에서 auto-deploy / scan-enabled 조정**  
    **(예 : scan-enabled="false"로 해두고 CLI나 관리 콘솔로 수동 배포)**
3.  **중요 데이터 저장 금지**  
    ****→**  배포 디렉토리는 컨텐츠 전용으로, 설정/데이터 등은 별도 경로에 보관**

* * *

**5\. 정리**

-   **원인 : 커스텀 배포 디렉토리를 지정하면 JBoss가 그 경로를 전적으로 소유 및 관리한다고 판단하여,**  
    **배포 라이프사이클 과정에서 디렉토리와 파일을 삭제/재생성함**
-   **해결책  
    **→****  **기본 경로 사용**  
    ****→**  커스텀 경로는 JBoss 전용으로만 사용**  
    ****→**  배포 스캐너 설정 조정으로 자동 삭제 방**
