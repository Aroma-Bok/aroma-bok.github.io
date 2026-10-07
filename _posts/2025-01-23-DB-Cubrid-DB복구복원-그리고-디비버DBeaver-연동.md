---
title: "[DB] Cubrid DB복구,복원 그리고 디비버(DBeaver) 연동"
date: 2025-01-23 13:59:28 +0900
categories: ["Solution & Tools", "DB"]
tags: ["cubrid restoredb", "cubrid 데이터베이스 복구", "쿠브리드 복구 디비버 연동", "큐브리드 디비 복구", "큐브리드 복원"]
tistory_url: "https://aroma-bok.tistory.com/entry/DB-Cubrid-DB%EB%B3%B5%EA%B5%AC%EB%B3%B5%EC%9B%90-%EA%B7%B8%EB%A6%AC%EA%B3%A0-%EB%94%94%EB%B9%84%EB%B2%84DBeaver-%EC%97%B0%EB%8F%99"
---
**사용한 버전은 Cubrid 10.1 | 11.2 버전 기준**

* * *

#### **1\. Cubrid 복구 데이터베이스 준비**

**▶ 이미지 상에서 bk 폴더 안에 bk 파일이 존재**

![](/assets/img/posts/303/1.png)

*bk폴더안에 bk 파일 존재*

#### **2\. Cubrid 환경변수 세팅**

![](/assets/img/posts/303/2.png)

#### **3\. Cubrid 복구 데이터베스 복구 명령어 실행**

****▶** NAS를 이용해서 설치폴더와 실제 Database 위치는 다르게 지정**

**이때, databases.txt 위치는 실제 Database가 위치한 NAS에 위치해야 한다.**

**큐브리드 설치 위치**  **\- /dbms**

**databases.txt 위치 - /NAS/cubrid\_databases/10.1**

![](/assets/img/posts/303/3.png)

*큐브리드가 실제 설치된 경로 - /dbms*

![](/assets/img/posts/303/4.png)

*databases.txt가 실제 존재하는 위치 - /NSDP/cubrid\_databases/10.1*

![](/assets/img/posts/303/5.png)

*복구 명령어 실행 + 복구 완료*

#### **일반적인 복구 명령어** 

```bash
// 기본 명령어
cubrid restoredb [options] database_name
```

**option을 사용한 복구 명령어**

```bash
// 내가 사용한 명령어
cubrid restoredb -u -B /NSDP/cubrid_databases/11.2/AUTH-DB5/AUTH_bk0v000 AUTH
```

**\-u : databases.txt 파일에 등록된 위치에 데이터베이스를 복구**

**\-b : 백업파일이 위치하는 디렉터리 경로를 직접 지정**

**▣ 다양한 옵션들이 많기에 필요시 Cubrid 공식 페이지 참고**

> **\-u 옵션을 사용하기에, databases.txt 파일에 등록된 위치에 데이터베이스를 복구하는 거라서**  
> **미리 databases.txt 파일에 아래처럼 작성이 필요**  
>   
> **db\_name / db\_directory / server\_host / logfile\_directory  
> **  
> **db\_name: 데이터베이스 이름**  
> **db\_directory: 데이터베이스 파일이 저장된 디렉터리 경로**  
> **server\_host: 데이터베이스 서버가 실행되는 호스트 이름**  
> **logfile\_directory: 로그 파일이 저장되는 디렉터리 경로**  
>   
> **DB /NAS/cubrid\_databases/10.1/DB localhost /NSDP/cubrid\_databases/10.1/DB**

#### **4\. 복구된 데이터베이스 실행 및 상태 확인**

**\- cubrid service start****\- cubrid server start dbname**

![](/assets/img/posts/303/6.png)

![](/assets/img/posts/303/7.png)

#### **4\. Dbeaver에서 접속**

![](/assets/img/posts/303/8.png)
