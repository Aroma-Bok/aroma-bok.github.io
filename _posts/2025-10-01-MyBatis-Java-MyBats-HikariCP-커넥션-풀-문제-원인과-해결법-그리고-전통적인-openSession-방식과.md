---
title: "[MyBatis] Java MyBats HikariCP 커넥션 풀 문제 원인과 해결법 그리고 전통적인 openSession 방식과 DI 방식 개념 비교"
date: 2025-10-01 17:50:20 +0900
categories: ["Solution & Tools", "MyBatis"]
tags: ["connection is not available request timed out after", "opensession vs di 방식 차이", "전통적인 opensession 방식", "커넥션 풀 문제 해결 방법"]
tistory_url: "https://aroma-bok.tistory.com/entry/MyBatis-Java-MyBats-HikariCP-%EC%BB%A4%EB%84%A5%EC%85%98-%ED%92%80-%EB%AC%B8%EC%A0%9C-%EC%9B%90%EC%9D%B8%EA%B3%BC-%ED%95%B4%EA%B2%B0%EB%B2%95-%EA%B7%B8%EB%A6%AC%EA%B3%A0-%EC%A0%84%ED%86%B5%EC%A0%81%EC%9D%B8-openSession-%EB%B0%A9%EC%8B%9D%EA%B3%BC-DI-%EB%B0%A9%EC%8B%9D-%EA%B0%9C%EB%85%90-%EB%B9%84%EA%B5%90"
---
#### **Java MyBats HikariCP 커넥션 풀 문제 원인과 해결법**

-   **Java 환경에서 MyBats와 HikariCp 커넥션 풀을 사용할 때, 커넥션 풀 고갈 및 타임아웃 문제가 종종 발생**
-   **주로 커넥션 누수, 잘못된 트랜잭션 범위 설정, 반복적 커밋/반환 등으로 인한 자원관리 부실에서 비롯**

* * *

#### **1\. 커넥션 풀과 커넥션 반환**

-   **커넥션 풀은 DB 서버와의 커넥션을 재활용해 성능을 높임**
-   **커넥션을 사용 후 반드시 반환해야 다른 요청들이 사용 가능**
-   **반환이 누락되면 누적되어 커넥션 풀이 고갈되고 요청 시 대기 또는 타임아웃 발생**

```java
Exception in thread "restartedMain" java.lang.reflect.InvocationTargetException
at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:118)
at java.base/java.lang.reflect.Method.invoke(Method.java:580)
at org.springframework.boot.devtools.restart.RestartLauncher.run(RestartLauncher.java:50)
Caused by: org.apache.ibatis.exceptions.PersistenceException: 
### Error querying database.  Cause: org.springframework.jdbc.CannotGetJdbcConnectionException: Failed to obtain JDBC Connection
### The error may exist in file [C:~~~.xml]
### The error may involve kr.go.sh.mapper.tibero.mapper.select~
### The error occurred while executing a query
### Cause: org.springframework.jdbc.CannotGetJdbcConnectionException: Failed to obtain JDBC Connection
at org.apache.ibatis.exceptions.ExceptionFactory.wrapException(ExceptionFactory.java:30)
at org.apache.ibatis.session.defaults.DefaultSqlSession.selectList(DefaultSqlSession.java:156)
at org.apache.ibatis.session.defaults.DefaultSqlSession.selectList(DefaultSqlSession.java:147)
at org.apache.ibatis.session.defaults.DefaultSqlSession.selectList(DefaultSqlSession.java:142)
at org.apache.ibatis.binding.MapperMethod.executeForMany(MapperMethod.java:147)
at org.apache.ibatis.binding.MapperMethod.execute(MapperMethod.java:80)
at org.apache.ibatis.binding.MapperProxy$PlainMethodInvoker.invoke(MapperProxy.java:141)
at org.apache.ibatis.binding.MapperProxy.invoke(MapperProxy.java:86)
at jdk.proxy3/jdk.proxy3.$Proxy70.select~(Unknown Source)
at kr.go.sh.main.Service.select~(Service.java:230)
at kr.go.sh.main.Service.select~(Service.java:144)
at kr.go.sh.OpenDataToTiberoApplication.main(Application.java:71)
at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:103)
... 2 more
Caused by: org.springframework.jdbc.CannotGetJdbcConnectionException: Failed to obtain JDBC Connection
at org.springframework.jdbc.datasource.DataSourceUtils.getConnection(DataSourceUtils.java:84)
at org.mybatis.spring.transaction.SpringManagedTransaction.openConnection(SpringManagedTransaction.java:77)
at org.mybatis.spring.transaction.SpringManagedTransaction.getConnection(SpringManagedTransaction.java:64)
at org.apache.ibatis.executor.BaseExecutor.getConnection(BaseExecutor.java:348)
at org.apache.ibatis.executor.BatchExecutor.doQuery(BatchExecutor.java:91)
at org.apache.ibatis.executor.BaseExecutor.queryFromDatabase(BaseExecutor.java:336)
at org.apache.ibatis.executor.BaseExecutor.query(BaseExecutor.java:158)
at org.apache.ibatis.executor.CachingExecutor.query(CachingExecutor.java:110)
at org.apache.ibatis.executor.CachingExecutor.query(CachingExecutor.java:90)
at org.apache.ibatis.session.defaults.DefaultSqlSession.selectList(DefaultSqlSession.java:154)
... 13 more
Caused by: java.sql.SQLTransientConnectionException: HikariPool-1 - Connection is not available, request timed out after 30013ms (total=10, active=10, idle=0, waiting=0)
at com.zaxxer.hikari.pool.HikariPool.createTimeoutException(HikariPool.java:686)
at com.zaxxer.hikari.pool.HikariPool.getConnection(HikariPool.java:179)
at com.zaxxer.hikari.pool.HikariPool.getConnection(HikariPool.java:144)
at com.zaxxer.hikari.HikariDataSource.getConnection(HikariDataSource.java:127)
at org.springframework.jdbc.datasource.DataSourceUtils.fetchConnection(DataSourceUtils.java:160)
at org.springframework.jdbc.datasource.DataSourceUtils.doGetConnection(DataSourceUtils.java:118)
at org.springframework.jdbc.datasource.DataSourceUtils.getConnection(DataSourceUtils.java:81)
... 22 more
```

#### **2\. SqlSession과 트랜잭션 범위**

-   **MyBatis의 SqlSession은 하나의 DB 연결 세션으로서 트랜잭션 단위**
-   **트랜잭션 커밋/롤백 시 Sql Session이 중요한 역할**
-   **너무 넓거나 너무 자주 커믹하면 리소스 부담 및 관리 어려움 발생**

#### **3\. try~with~resources의 역할**

-   **Java 문법으로, 리소스를 열고 닫는 코드를 자동화**
-   **SqlSession을 try~with~resources로 감싸서 자동으로 close()와 rollback 처리**

* * *

**잘못된 코드 예시 (원본 코드)**

```java
public void selectList(String param1, String param2 , String param3 ,String param4) throws Exception{

    SqlSession sqlSession = oracleBatchSessionTemplate.getSqlSessionFactory().openSession(ExecutorType.BATCH);
    Mapper mapper = sqlSession.getMapper(Mapper.class);
    
    // ... 중략 ...
    
    for(Map<String,Object> m : list1) {
        // ... 중략 ...
        for(Map<String,Object> fileMap : list2) {
            // ... 중략 ...

            mapper.insert(logMap);
            sqlSession.commit(); // 반복문 내에서 매번 커밋 호출
        }
    }
}
```

**문제점**

-   **SqlSession을 열고 닫는 구조가 부재하여 커넥션이 풀에 반환되지 않음 → 누수 발생 가능**
-   **파일별로 반복 커밋 → DB와 자원 부하 증가, 커넥션 점유 시간이 길어짐**
-   **예외 발생 시 rollback 불명확 → 트랜잭션 상태 불안정**

**수정된 코드 예시 (개선 코드)**

```java
public void selectList(String param1, String param2 , String param3 , String param4) throws Exception {
    try (SqlSession sqlSession = oracleBatchSessionTemplate.getSqlSessionFactory().openSession(ExecutorType.BATCH)) {
        Mapper mapper = sqlSession.getMapper(Mapper.class);

        // ... 중략: select 및 파일 처리 등 ...
        for (Map<String,Object> m : dtinSetMtList) {
            // ... 중략 ...
            for (Map<String,Object> fileMap : filteredFileList) {
                // ... 중략 ...
                
                Mapper.insert(logMap);
                // 반복 commit 제거
            }
        }
        sqlSession.commit(); // 루프 종료 후 단 한 번만 커밋
        saveFailedListToJson(param1);
    } // try-with-resources로 자동 close, 예외 시 rollback 처리
}
```

#### **개선 효과**

-   **try~with~resources로 자동 커넥션 반환과 예외 시 rollback 보장**
-   **커밋을 한 번만 처리해 DB 부담 감소 및 커넥션 점유 시간 최소화**
-   **자원 누수 및 풀 고갈 문제 예방**

* * *

#### **개념적 비교**

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft"><tbody><tr><td style="width: 23.9147%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">항목</span></b></span></td><td style="width: 34.4961%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">원본 코드</span></b></span></td><td style="width: 41.5891%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">개선 코드</span></b></span></td></tr><tr><td style="width: 23.9147%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">커넥션 반환</span></b></span></td><td style="width: 34.4961%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">명시적 반환 또는 close 없음</span></b></span></td><td style="width: 41.5891%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">try~with~resources 로 자동반환</span></b></span></td></tr><tr><td style="width: 23.9147%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">커밋 위치</span></b></span></td><td style="width: 34.4961%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">반복문 내 다수 번 수행</span></b></span></td><td style="width: 41.5891%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">반복문 종료 후 한 번만</span></b></span></td></tr><tr><td style="width: 23.9147%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">트랜잭션 안정성</span></b></span></td><td style="width: 34.4961%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">예외 시 rollback 처리 불명확</span></b></span></td><td style="width: 41.5891%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">try~with~resources에서 자동 rollback 지원</span></b></span></td></tr><tr><td style="width: 23.9147%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">자원 관리 효율성</span></b></span></td><td style="width: 34.4961%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">낮음 - 누수/고갈 및 과도한 DB 부가 가능</span></b></span></td><td style="width: 41.5891%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">높음 - 자원 재활용 최적화</span></b></span></td></tr></tbody></table>

#### **결론**

-   **커넥션 풀 문제는 자바에서 리소스 자동 관리와 적절한 트랜잭션 범위 설정이 핵심**
-   **MyBatis와 HikariCp 같이 커넥션 풀을 사용하는 환경에서 SqlSession은 반드시 try~with~resources로 관리하고 커밋은 업무 단위에 맞게 한 번만 수행해야 효율적**
-   **이 원칙을 지키면 커넥션 누수와 타임아웃을 방지하고, 안정적인 DB 작업 환경을 마련 가능**

* * *

#### **MyBatis에서 전통적인 openSession() 방식과 현대적인 DI 및 SqlSessionTemplate 방식 비교**

#### **1\. 전통적 openSession() 방식 (직접 SqlSession 관리)**

```java
public class UserDaoImpl {

    private SqlSessionFactory sqlSessionFactory;

    public void setSqlSessionFactory(SqlSessionFactory sqlSessionFactory) {
        this.sqlSessionFactory = sqlSessionFactory;
    }

    public User getUserById(String userId) {
        SqlSession session = sqlSessionFactory.openSession();
        try {
            return session.selectOne("namespace.UserMapper.selectUser", userId);
        } finally {
            session.close(); // 반드시 닫아야 함
        }
    }
}
```

**특징 및 문제점**

-   **개발자가 openSession() 호출해 SqlSession을 직접 열고 닫아야 함**
-   **닫지 않으면 커넥션 누수 발생 가능**
-   **트랜잭션과 자원 관리를 직접 처리**
-   **복잡도 증가, 예외처리 미흡 시 안정성 저하**
-   **스레드 안정성이 보장되지 않음**

#### **2\. DI 방식 및 SqlSessionTemplate 사용 (스프링 MyBatis 연동)**

```java
@Repository
public class UserDaoImpl {

    @Autowired
    private SqlSessionTemplate sqlSessionTemplate;

    public User getUserById(String userId) {
        return sqlSessionTemplate.selectOne("namespace.UserMapper.selectUser", userId);
    }
}
```

**특징 및 장점**

-   **SqlSessionTemplate이 SqlSession 인터페이스를 구현해 대신 사용**
-   **세션 생성, 커밋, 롤백, 닫기 등 자원 관리를 내부에서 자동 처리**
-   **트랜잭션과 커넥션 풀 연동도 프레임워크가 담당**
-   **스레드 안전하며 여러 DAO에서 공유 가능**
-   **@Autowired 등 DI 컨테이너가 SqlSessionTemplate 주입**
-   **코드 간결하고 안정성 높음**

#### **3\. 개념 비교**

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft"><tbody><tr><td style="width: 20.5426%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">구분</span></b></span></td><td style="width: 35.8914%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">전통적 openSession 방식</span></b></span></td><td style="width: 43.5659%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">DI 방식 (SqlSessionTemplate)</span></b></span></td></tr><tr><td style="width: 20.5426%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">SqlSession 획득</span></b></span></td><td style="width: 35.8914%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">직접 openSession() 호출</span></b></span></td><td style="width: 43.5659%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">DI 컨테이너가 SqlSessionTemplate 주입</span></b></span></td></tr><tr><td style="width: 20.5426%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">커넥션 반환</span></b></span></td><td style="width: 35.8914%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">개발자가 직접 close() 호출 관리</span></b></span></td><td style="width: 43.5659%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">자동으로 세션과 커넥션 반환 관리</span></b></span></td></tr><tr><td style="width: 20.5426%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">트랜잭션 관리</span></b></span></td><td style="width: 35.8914%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">수동으로 commit/rollback 처리</span></b></span></td><td style="width: 43.5659%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">스프링 트랜잭션과 통합되어 선언적 관리 가능</span></b></span></td></tr><tr><td style="width: 20.5426%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">쓰레드 안전성</span></b></span></td><td style="width: 35.8914%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">보장 안 됨</span></b></span></td><td style="width: 43.5659%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">쓰레드 안전 (여러 DAO 및 매퍼 공유 가능)</span></b></span></td></tr><tr><td style="width: 20.5426%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">예외 처리</span></b></span></td><td style="width: 35.8914%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">직접 처리 필요</span></b></span></td><td style="width: 43.5659%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">예외 자동 변환 및 안정적 처리</span></b></span></td></tr><tr><td style="width: 20.5426%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">코드 복잡도</span></b></span></td><td style="width: 35.8914%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">높음</span></b></span></td><td style="width: 43.5659%;"><span style="color: #333333;"><b><span style="font-family: 'Noto Serif KR';">낮음</span></b></span></td></tr></tbody></table>

#### **4\. 정리**

-   **전통 방식은 저수준 API를 직접 다루므로 세션, 트랜잭션, 커넥션 관리를 개발자가 수동으로 해야 함**
-   **DI 방식은 스프링 등 프레임워크와 연동되어 이런 작업을 프레임워크가 대신 처리해 줌**
-   **DI 방식이 현대적이고 유지보수가 쉽고 안전하기 때문에 권장됨**
-   **직접 openSession을 쓰려면 꼭 try~with~resources 같이 자동 close 구조를 적용해 누수를 막아야 함**
