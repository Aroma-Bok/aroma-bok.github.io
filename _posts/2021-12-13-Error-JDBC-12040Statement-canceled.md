---
title: "[Error] JDBC-12040:Statement canceled"
date: 2021-12-13 20:50:57 +0900
categories: ["ETC", "Error"]
tags: ["jdbc-12040", "sql 오류", "statement canceled"]
tistory_url: "https://aroma-bok.tistory.com/entry/Error-JDBC-12040Statement-canceled"
---
```java
### Error updating database.  Cause: java.sql.SQLException: JDBC-12040:Statement canceled.
```

#### **StatementTimeout**

Statement 하나가 얼마나 오래 수행되어도 괜찮은지에 대한 한계 값이다. JDBC API인 Statement에 타임아웃 값을 설정하며, 이 값을 바탕으로 JDBC 드라이버가 StatementTimeout을 처리한다.

JDBC API인 java.sq.Statement.setQueryTimeout() 메서드로 설정한다.

필자가 진행하는 프로젝트에서는 value='10'으로 지정되어 있었다. 

쿼리는 테스트해봤지만 잘 돌아갔다. 다만, xml에 기입하여 기능을 구현하니까 계속 에러가 발생했다.

그래서 필자가 작성한 쿼리가 느린 탓인지 TimeOut이 자꾸 걸렸다...

처음엔 단순히 쿼리가 느려서인건가? 단순히 Performance 문제라고 생각을 했다.

하지만, 구글링을 하다가 더 비슷한 내용을 다루고 있는 내용을 찾아서 원인을 알게되었다.

```java
// 에러 발생 쿼리

INSERT INTO XXTable
     (A)
VALUES ((SELECT NVL(MAX(증가컬럼) + 1, 0) FROM XXTable))
```

**\* 증가 칼럼이 null이 아니면 +1씩 시켜서 그 값을 Insert 하려고 하였다. 하지만, 의도대로 돌아가지 않았다.**

**원인 : VALUES 인자로 스칼라 형태의 결과 값만 올 수 있습니다. (위의 쿼리는 테이블 형태의 결과를 줍니다.)  
**

**\*출처 : [https://ooz.co.kr/80](https://ooz.co.kr/80)**

**추가 설명 : VALUES 자리에 직접 값만 들어와야 하고,서브쿼리는 허용되지 않기 때문에 발생하는 제약**

```java
// 정상 작동 쿼리

INSERT INTO XXTable
     (A)
SELECT ISNULL(MAX(증가컬럼) + 1, 0) FROM XXTable
```

이렇게하니까 문제를 해결했다..

신기..
