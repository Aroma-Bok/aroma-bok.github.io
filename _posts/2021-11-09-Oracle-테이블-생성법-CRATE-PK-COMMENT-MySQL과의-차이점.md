---
title: "[Oracle]  테이블 생성법 (CRATE, PK, COMMENT) + MySQL과의 차이점"
date: 2021-11-09 23:01:35 +0900
categories: ["Solution & Tools", "DB"]
tags: ["db", "mysql", "oracle", "sql", "주니어개발자", "차이점"]
tistory_url: "https://aroma-bok.tistory.com/entry/Oracle-%ED%85%8C%EC%9D%B4%EB%B8%94-%EC%83%9D%EC%84%B1%EB%B2%95-CRATE-PK-COMMENT-MySQL%EA%B3%BC%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90"
---
MySQL만 사용하다가 Oracle을 처음 접하게 되면서 약간의 문법의 차이를 느끼게 되었다. 그래서 정리하게 되었다.

* * *

MySQL

```sql
CREATE TABLE TEST (
	A INT(10) NOT NULL AUTO_INCREMENT PRIMARY KEY COMMENT 'A 컬럼',
    B VARCHAR(10) NOT NULL COMMENT 'B 컬럼',
    C DATE COMMENT 'C 컬럼'
    ) ENGINE=INNODB DEFAULT CHARSET=utf8 COMMENT='테스트 테이블'
```

-   TABLE 생성시 COMMENT 같이 지정 가능
-   AUTO\_INCREMENT , PRIMARY KEY 같이 쉽게 지정 가능

ORACLE

```sql
CREATE TABLE TEST(
 A VARCHAR2(10) NOT NULL,
 B DATE
 CONSTRAINT TEST_pk PRIMARY KEY (A)
 );
 
 COMMENT ON TABLE TEST IS '테스트컬럼';
 COMMENT ON COLUMN TEST.A IS 'A컬럼';
 COMMENT ON COLUMN TEST.B IS 'B컬럼';
```

-   COMMENT는 테이블을 생성하고 지정해줄 수 있음
-   AUTO\_INCREMENT 기능 없음

정말 간단하게 이정도 차이가 있다는 것을 알아봤다. 알아보면 더 많은 차이들이 있을 것이다.

어떤 게 더 좋다는 것보다는 목적에 맞게 필요한 것을 쓰는 게 좋지 않을까 생각이 든다. 

참 공부할게 많다...
