---
title: "[DB] Replication에 대해서..."
date: 2024-12-24 16:02:32 +0900
categories: ["Solution & Tools", "DB"]
tags: ["db 복제", "replication 개념", "replication 동작원리", "replication 이란", "레플리케이션이란"]
tistory_url: "https://aroma-bok.tistory.com/entry/DB-Replication%EC%97%90-%EB%8C%80%ED%95%B4%EC%84%9C"
---
### **Replication**

**▶ 데이터를 한 데이터베이스 서버에서 다른 데이터베이스 서버로 복사하여 동기화 상태로 유지하는 기능**

****▶** 데이터 가용성을 높이고, 읽기 성능 향상, 백업, 부하 분산 등 다양한 목적을 위해 사용**

* * *

#### **Replication의 주요 개념**

#### **1\. Master-Slave 구조**

-   **Master 서버  : 데이터를 기록(쓰기 작업)하는 주 데이터베이스**
-   **Slave 서버 : Master 서버로부터 데이터를 복사하여 읽기 전용 또는 백업 목적으로 사용**

#### **2\. Binlog (Binary Log) 구조**

-   Ma**ster 서버는 Binlog(바이너리 로그)를 통해 데이터 변경 작업(Insert, Update, Delete 등)을 기록**
-   **Slave 서버는 Binlog를 읽어 동일한 변경 작업을 수행하여 Master와 데이터를 동기화**

#### **3\. Replication Threads**

-   **I/O Thread : Slave 서버에서 Master 서버의 Binlog를 읽어 Relay Log에 저장**
-   **SQL Thread  : Relay Log에 저장된 변경 작업을 Slave 서버에 적용**

#### **4\. Asynchronous, Semi-Synchronous, Synchronous Replication**

-   **Asynchronous Replication**  
    **Master 서버가 변경 작업이 Slave 서버에 적용되었는지 확인하지 않음**  
    **기본 방식으로 지연(latency)이 발생할 수 있음**
-   **Semi-Synchronous Replication**  
    **Master 서버는 최소 한 개의 Slave 서버로부터 변경 작업이 적용되었다는 확인을 받아야 작업이 완료**
-   **Synchronous Replication**  
    **모든 Slave 서버가 변경 작업을 완료한 후 Master 서버가 작업을 완료(매우 드문 사용 사례)**

* * *

#### **Replication의 동작 원리**

1.  **Master 서버에서 데이터 변경 작업 발생 (예: Insert, Update)**
2.  **Master 서버가 변경 내용을 BinLog에 기록**
3.  **Slave 서버가 Master 서버의 BinLog를 읽어 Relay Log에 저장(I/O Thread)**
4.  **Slave 서버가 Relay Log를 처리하여 변경 작업을 데이터베이스에 적용 (SQL Thread)**

* * *

#### **Replication의 주요 사용 사례**

#### **1. 읽기 부하 분산 (Read Scalability)**

-   **Slave 서버를 읽기 전용으로 사용하여 Master 서버의 읽기 부하를 줄일 수 있다**
-   **읽기 작업이 많은 애플리케이션에서 성능 향상을 기대할 수 있다**

#### **2\. 고가용성 (High Availability)**

-   **Master 서버에 장애가 발생하면 Slave 서버 중 하나를 Master로 승격(Promote)하여 서비스 중단을 방지할 수 있다.**

#### **3\. 백업(Backup)**

-   **Slave 서버에서 백업 작업을 수행하여 Master 서버의 부하를 줄이고 가용성을 보장한다.**

#### **4\. 데이터 분석(Data Analytics)**

-   **실시간 트랜잭션 데이터를 Master 서버에서 처리하고, 분석 작업을 Slave 서버에서 수행하여 성능을 분리한다.**

* * *

#### **Replication의 한계**

#### **1. 데이터 지연(Replication Lag)**

-   **Slave 서버가 Master 서버의 Binlog를 처리하는데 시간이 걸릴 수 있다**
-   **특히 트래픽이 많은 경우 지연이 발생할 가능성이 높다**

#### **2. Master 서버 단일 장애점 (Single Point of Failure)**

-   **Master 서버가 다운되면 전체 데이터 쓰기 작업이 중단될 수 있다**
-   **이를 보완하려면 Multi-Master Replication이나 자동 장애 복구 시스템을 구축해야 한다**

#### **3\. 충돌관리**

-   **Multi-Master Replication의 경우, 같은 데이터에 대한 충돌(Conflict)을 관리하기 어렵다**

#### **4\. 수동관리 필요**

-   **Master-Slave 구성을 유지하려면 수동으로 서버를 관리하고 모니터링해야 하므로 관리 오버헤드가 증가한다**

* * *

**Replication의 한계를 보완하기 위해서 요즘은 클러스터(Cluster)를 사용하여 고가용성, 자동 장애 복구,  
데이터 동기화 등을 제공한다**
