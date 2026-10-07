---
title: "DB GRANT, REVOKE 권한 개념 [Specified schema object was not found. ]"
date: 2023-08-28 08:58:41 +0900
categories: ["Solution & Tools", "DB"]
tags: ["grant", "revoke", "specified schema object was not found."]
tistory_url: "https://aroma-bok.tistory.com/entry/DB-GRANT-REVOKE-%EA%B6%8C%ED%95%9C-%EA%B0%9C%EB%85%90"
---
**DBeaver나 Tibero에서 조회하면 해당 테이블의 데이터가 잘 조회되는데, MyBatis를 통해서 DB에 붙어 데이터를 조회하면**

**해당 스키마를 찾을 수 없다고 콘솔에 찍히고 있던 경험이 있다. (Specified schema object was not found.)**

**결과적으로는 사용자 권한이 달라서 조회가 안 되는 문제였다. 테이블 권한에 간과하고 있던 나에게 일어난 작은 에피소드로 인해서 권한에 대해 간단하게 정리하게 되었다.**

* * *

#### **권한**

**데이터베이스 객체 (테이블, 뷰, 프로시저 등)에 대한 다양한 작업을 수행하는데 필요한 권한을 의미한다.**

**GRANT 문을 사용하여 사용자 또는 역할에게 권한을 부여할 수 있으며, 부여된 권한을 사용하여 데이터베이스 객체를**

**조작하거나 쿼리 할 수 있다.**

* * *

**권한부여**

```sql
GRANT privilege [, privilege, ...] ON object TO user [WITH GRANT OPTION];
```

-   **privilege : 부여할 권한의 종류 \[Ex - SELECT, INSERT, UPDATE, DELETE\]**
-   **object : 권한을 부여할 대상 객체 (테이블, 뷰, 프로시저 등)**
-   **user : 권한을 받을 사용자 지정**
-   **WITH GRANT OPTION : 권한을 부여받은 사용자가 다른 사용자에게 부여받은 권한을 부여할 수 있는 옵션**

```sql
// SELECT
GRANT SELECT ON employees TO user1;
// INSERT
GRANT INSERT ON employees TO user1;
// DELETE
GRANT DELETE ON employees TO user1;
// UPDATE
GRANT UPDATE ON employees TO user1;
```

* * *

**권한회수**

**REVOKE 문을 사용하여 권한을 회수할 수 도 있다.**

```sql
REVOKE privilege [, privilege, ...] ON object FROM user;
```

* * *

**GRANT, REVOKE문을 사용하여 데이터베이스 객체에 대한 권한을 관리함으로써 데이터베이스 보안과 접근 제어를**

**유지할 수 있다.**
