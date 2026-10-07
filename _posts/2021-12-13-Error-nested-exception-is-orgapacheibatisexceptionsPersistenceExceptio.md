---
title: "[Error] nested exception is org.apache.ibatis.exceptions.PersistenceException / IllegalArgumentException"
date: 2021-12-13 20:34:46 +0900
categories: ["ETC", "Error"]
tags: ["illegalargumentexception", "persistenceexception", "sql 오류"]
tistory_url: "https://aroma-bok.tistory.com/entry/Error-nested-exception-is-orgapacheibatisexceptionsPersistenceException-IllegalArgumentException"
---
**\[Error\] nested exception is org.apache.ibatis.exceptions.PersistenceException / IllegalArgumentException**

* * *

```java
nested exception is org.apache.ibatis.exceptions.PersistenceException: 
### Error updating database. Cause: java.lang.IllegalArgumentException: 
Mapped Statements collection does not contain value for XXXMaster.XXTable_INSERT### Cause: 
java.lang.IllegalArgumentException:
Mapped Statements collection does not contain value for XXXMaster.XXTable_INSERT
```

#### **원인**

1.  **mapper id가 틀린 경우**
2.  **Parameter와 bean의 field명이 틀린 경우**
3.  **sql.xml에서 정의된 namespace와 DAO에서 호출하는 namespace가 다를 경우**
4.  **mapper가 정의되어 있지 않거나 Speeling이 틀린 경우**
5.  **mappe에 정의된 namespace 명칭이 같은 Application 내에 중복될 경우**

필자는 3번에 속한 문제로 에러가 발생했다. 

Service에서 호출하는 부분에서 namespace를 제대로 작성하지 않아서 오류가 발생했었다.

\*출처 : [https://jamong-icetea.tistory.com/67](https://jamong-icetea.tistory.com/67)
