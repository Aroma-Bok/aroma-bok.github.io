---
title: "[MySQL] Unable to load authentication plugin 'caching_sha2_password'"
date: 2022-11-18 13:45:28 +0900
categories: ["ETC", "Error"]
tags: ["caching_sha2_password", "unable to load authentication plugin 'caching_sha2_password'."]
tistory_url: "https://aroma-bok.tistory.com/entry/MySQL-the-last-packet-sent-successfully-to-the-server-was-0-milliseconds-ago-cachingsha2password"
---
**ERROR : Unable to load authentication plugin 'caching\_sha2\_password'.** 

**MySQL8.0 이상부터 발생하는 문제로써 8.0 버전의 기본 인증 플러그인 변경으로 발생한 문제.**

**SHA-256 hasing을 구현하는 두 가지 인증 플러그인을 지원.**

**1\. SHA-256 : 기본적인 SHA-256 인증을 구현한 플러그인.**

**2\. caching\_sha2\_password : sha256\_password와 동일하지만, 성능 향상을 위해 서버 캐싱을 이용.**

**MySQL 8.0의 기본 인증 플러그인은 caching\_sha2\_password이다.**

**caching\_sha2\_password을**

**그렇기 때문에 '보안 연결'이나 'RSA'보안을 적용하지 않으면 해당 에러가 발생.**

**이 문제를 해결하기 위해 가장 빠른 방법은 인증 방식을 MySQL5.\* 버전에서 디폴트로 사용되었던 'mysql\_native\_password' 방식으로 변경하는 것.** 

**밑에는 그 방식을 적용하는 방법을 나타낸 것이다.**

**Resolve :** 

```sql
mysql -u root -p
Enter password: ********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 41
Server version: 8.0.31 MySQL Community Server - GPL

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```

```sql
use mysql
Database changed
```

```sql
ALTER USER '아이디'@'localhost' IDENTIFIED WITH mysql_native_password BY '비밀번호';
Query OK, 0 rows affected (0.01 sec)
```

```sql
flush privileges;
Query OK, 0 rows affected (0.05 sec)
```
