---
title: "ResultSet vs ResultMap"
date: 2023-06-30 11:05:36 +0900
categories: ["Solution & Tools", "MyBatis"]
tags: ["resultmap", "resultset", "resultset resultmap 차이"]
tistory_url: "https://aroma-bok.tistory.com/entry/ResultSet-vs-ResultMap"
---
### **ResultSet vs ResultMap**

```sql
#{department, mode=OUT, jdbcType=CURSOR, javaType=ResultSet, resultMap=departmentResultMap}
```

**mode속성은 IN, OUT 또는 INOUT 파라미터를 명시하기 위해 사용한다.**

**파라미터가 OUT 또는 INOUT이라면 파라미터의 실제 값은 변경될 것이다.** 

**mode=OUT(또는 INOUT)이고 jdbcType=CURSOR(예를 들어 오라클 REFCURSOR)라면**

**파라미터의 타입에 ResultSet를 매핑하기 위해 resultMap을 명시해야만 한다.**

* * *

#### **ResultSet**

**JDBC(Java Database Connectivity) API에서 사용되는 인터페이스**

  
**JDBC를 사용하여 데이터베이스로부터 쿼리의 결과를 가져오는 데 사용되는 인터페이스로,**

**결과 집합을 순회하고 데이터를 읽을 수 있게 도와준다.**

#### **ResultMap**

**MyBatis에서 데이터베이스의 쿼리 결과와 자바 객체를 매핑하기 위해 사용되는 객체로,**

**데이터베이스 컬럼과 자바 객체의 필드를 연결하여 매핑 정보를 정의한다.**

**이를 통해 개발자는 데이터베이스와 자바 객체 간의 변환 작업을 간편하게 처리할 수 있다.**
