---
title: "APM(Application Performance Monitoring)과 스카우터(Scouter) 개념 및 JBoss 적용"
date: 2025-12-05 10:24:29 +0900
categories: ["Solution & Tools", "APM"]
tags: ["apm", "apm이란", "scouter", "스카우터", "스카우터 장단점"]
tistory_url: "https://aroma-bok.tistory.com/entry/APMApplication-Performance-Monitoring%EA%B3%BC-%EC%8A%A4%EC%B9%B4%EC%9A%B0%ED%84%B0Scouter-%EA%B0%9C%EB%85%90-%EB%B0%8F-JBoss-%EC%A0%81%EC%9A%A9"
---
**프로젝트 진행할 때, APM (스카우터)을 설치하는 업무가 생긴 경험이 있다. 한 번도 해본 적이 없어서,**

**개념도 같이 정리하기 위해 작성하였다.**

* * *

#### **APM(Application Performance Monitoring)**

-   **애플리케이션의 성능과 상태를 모니터링하고 분석하는 도구**
-   **애플리케이션의 병목 현상, 오류, 성능 저하를 빠르게 발견하고 해결하는 것이 목적**

**주요 기능**

-   **실시간 모니터링  
    ****\- CPU, 메모리, 트래픽, DB 쿼리 속도 등 애플리케이션 상태 실시간 확인**
-   **트랜잭션 추적  
    ****\- 사용자 요청이 애플리케이션 내 여러 서비스와 DB를 거치는 경로 추적**
-   **알람/통지  
    ****\- 특정 기준 초과 시, 관리자에게 알람**
-   **성능 분석  
    ****\- 느린 메서드, 쿼리, 외부 API 호출 등을 분석하여 최적화 포인트 제공**
-   **로그 통합  
    ****\- 애플리케이션 로그와 성능 데이터를 통합 분석**

**APM의 필요성**

-   **장애 대응 시간 단축**
-   **사용자 경험 개선**
-   **리소스 효율적 관리**
-   **시스템 확장성, 안정성 확보**

* * *

#### **스카우터(Scouter)**

-   **Java 기반 오픈소스 APM 도구**
-   **서버와 클라이언트/에이전트 구조로 동작하며 애플리케이션 성능, 트랜잭션, 시스템 상태를 시각화**

![](/assets/img/posts/327/1.png)

**구성 요소**

-   **Agent  
    ****\- 애플리케이션에 설치  
    ****\- JVM, 메서드 호출, DB 쿼리, 외부 요청 등을 모니터링  
    ****\- 수집한 데이터를 Collector로 전송**
-   **Collector (Server)  
    ****\- Agent가 보낸 데이터를 수집, 저장, 처리  
    ****\- 통신 : TCP/UDP 사용**
-   **Client(Viewer)  
    ****\- 데이터를 시각화하여 보여주는 대시보드  
    ****\- 실시간 트래픽, 느린 메서드, 트랜잭션 흐름 확인 가능**
-   **Plugins  
    ****\- 특정 기술(DB, Redis, Kafka 등)에 대한 추가 모니터링 기능**

**특징**

-   **설치와 설정이 비교적 간단**
-   **실시간 모니터링과 히스토리 데이터 제공**
-   **Java 애플리케이션에 최적화**
-   **오픈소스라 비용 부담 적음**

* * *

#### **설치 및 사용**

**1\. Scouter 설치**

```bash
#서버에서 해당 명령어로 스카우터 설치
wget https://github.com/scouter-project/scouter/releases/download/v2.20.0/scouter-all-2.20.0.tar.gz
```

**2\. Agent 세팅**

```bash
### scouter java agent configuration sample
obj_name=WAS
net_collector_ip=199.199.199.199
net_collector_port=6100
#net_collector_udp_port=6100
net_collector_tcp_port=6100
#hook_method_patterns=sample.mybiz.*Biz.*,sample.service.*Service.*
#trace_http_client_ip_header_key=X-Forwarded-For
#profile_spring_controller_method_parameter_enabled=false
#hook_exception_class_patterns=my.exception.TypedException
#profile_fullstack_hooked_exception_enabled=true
#hook_exception_handler_method_patterns=my.AbstractAPIController.fallbackHandler,my.ApiExceptionLoggingFilter.handleNotFoundErrorResponse
#hook_exception_hanlder_exclude_class_patterns=exception.BizException
trace_http_enabled=true
trace_http_client_ip_header_key=X-Forwarded-For
hook_undertow_enabled=true
hook_jboss_enabled=true
xlog_enabled=true
xlog_sampling_rate=100
```

**3\. JBoss 설정 세팅**

```bash
if [ "x$JAVA_OPTS" = "x" ]; then
   JAVA_OPTS="-Xms1303m -Xmx1303m -XX:MetaspaceSize=96M -XX:MaxMetaspaceSize=256m -Djava.net.preferIPv4Stack=true"
  # JAVA_OPTS="$JAVA_OPTS -Djboss.modules.system.pkgs=$JBOSS_MODULES_SYSTEM_PKGS -Djava.awt.headless=true"
  JAVA_OPTS="$JAVA_OPTS -Djboss.modules.system.pkgs=$JBOSS_MODULES_SYSTEM_PKGS,scouter -Djava.awt.headless=true"
  # 여기에 스카우터 javaagent 옵션 추가
  JAVA_OPTS="$JAVA_OPTS -javaagent:/opt/scouter/agent.java/scouter.agent.jar"
  JAVA_OPTS="$JAVA_OPTS -Dscouter.config=/opt/scouter/agent.java/conf/scouter.conf"
  JAVA_OPTS="$JAVA_OPTS -Dscouter.agent.tcp_port=6100"
else
   echo "JAVA_OPTS already set in environment; overriding default settings with values: $JAVA_OPTS"
fi
```

**4\. Collector(Server) 설정**

```bash
net_tcp_listen_ip=0.0.0.0
net_udp_listen_ip=0.0.0.0

net_tcp_listen_port=6100
net_udp_listen_port=6100

obj_name_max_length=64
```

**5\. Was 및 Collector(Server) 기동**

```bash
#Was 기동

#Collector(Server) 기동
# /opt/scouter/server 에 있는 startup.sh 실행
./startup.sh
```

**6\. Client 설치 및 실행**

```bash
#깃허브에서 다운 가능
https://github.com/scouter-project/scouter/releases/
```

![](/assets/img/posts/327/2.png)

*ID / PW는 기본 admin / admin으로 설정되어 있음*

![](/assets/img/posts/327/3.png)

* * *

**장점**

1.  **오픈소스 & 무료**  
    **\- 라이센스 비용 부담 없이 사용 가능**  
    **\- 커뮤니티에서 플러그인, 팁 등 자료 활용 가능**
2.  **설치 및 구성 간편**  
    **\- Agent만 애플리케이션에 설치하면 기본 모니터링 가능**  
    **\- Collector와 Client 구성이 단순해 소규모 환경에도 적합**
3.  **실시간 모니터링**  
    **\- JVM, CPU, Memory, Thread 상태 실시간 확인**  
    **\- 트랜잭션 처리 시간, HTTP 요청/응답 실시간 추적 가능**
4.  **트랜잭션 & 성능 분석**  
    **\- 메서드 호출 계층(Call Tree) 추적**  
    **\- 느린 메서드 및 SQL 쿼리 식별 가능**  
    **\- Custom Metric 지원으로 특정 비즈니스 로직 모니터링 가능**
5.  **경량화**  
    **\- 시스템 리소스 부담이 적음**  
    **\- 대규모 서버보다는 중소 규모 환경에 적합**
6.  **히스토리 데이터 제공**  
    **\- 시간별/일별 성능 통계 확인 가능**  
    **\- 장기 성능 분석 및 최적화에 활용 가능**

**단점**

1.  **Java 중심**  
    **\- Java 애플리케이션에 최적화되어 있음**  
    **\- Python, Node.js, .NET 등 다른 언어 지원 부족**
2.  **UI/시각화 한계**  
    **\- 기본 Web UI가 다소 단순함**  
    **\- 고급 분석이나 대시보드 커스터마이징이 제한적**
3.  **대규모 환경 한계**  
    **\- 많은 Agent와 서버가 동시에 모니터링될 경우 Collector 성능에 부담**  
    **\- 고가용성 환경 구성 시 별도 튜닝 필요**
4.  **전문 APM 기능 부족**  
    **\- 사용 APM (예: New Relic, Dynatrace, AppDynamics) 대비**  
    **1) 자동 병목 분석**  
    **2) AI 기반 이상 감지**  
    **3) 클라우드 환경 통합 모니터링 등의 고급 기능 부족**
5.  **커뮤니티 지원 제한**  
    **\- 오픈소스 특성상 공식 지원은 제한적**  
    **\- 문제 발생 시 직접 분석하거나 커뮤니티 도움 필요**
