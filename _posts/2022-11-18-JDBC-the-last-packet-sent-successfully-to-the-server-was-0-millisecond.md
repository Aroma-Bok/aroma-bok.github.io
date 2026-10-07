---
title: "[JDBC] the last packet sent successfully to the server was 0 milliseconds ago"
date: 2022-11-18 13:51:15 +0900
categories: []
tags: ["the last packet sent successfully to the server was 0 milliseconds ago"]
tistory_url: "https://aroma-bok.tistory.com/entry/JDBC-the-last-packet-sent-successfully-to-the-server-was-0-milliseconds-ago"
---
**ERROR : the last packet sent successfully to the server was 0 milliseconds ago** 

**MySQL은 SSL 설정이 default가 true인데, SSL 연결을 한다는 설정을 false로 변경해줘야 한다.**

**Resolve : DB URL뒤에 'useSSL=false' 추가해주면 된다.**

**파라미터가 여러 개라면 '&' 연결해준다.**

**변경 전**

```sql
ds.setUrl("jdbc:mysql://localhost/new_schema?characterEncoding=utf8");
```

**변경 후**

```sql
ds.setUrl("jdbc:mysql://localhost/new_schema?characterEncoding=utf8&useSSL=false");
```
