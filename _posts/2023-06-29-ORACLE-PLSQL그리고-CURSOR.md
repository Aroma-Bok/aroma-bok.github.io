---
title: "[ORACLE] PL/SQL그리고 CURSOR"
date: 2023-06-29 15:53:27 +0900
categories: ["Solution & Tools", "DB"]
tags: ["cursor 개념", "cursor란", "pl/sql 개념", "동적 cursor", "동적 커서"]
tistory_url: "https://aroma-bok.tistory.com/entry/ORACLE-PLSQL%EA%B7%B8%EB%A6%AC%EA%B3%A0-CURSOR"
---
**Cursor를 알기 전에 먼저 PL/SQL에 대한 개념을 알고 가는 게 좋을 것 같다.**

### **PL/SQL**

**PL/SQL은 데이터베이스 애플리케이션을 개발하기 위한 프로그래밍 언어, 즉 SQL을 확장한 절차적 언어이고,**

**프로시저는 PL/SQL에서 사용되는 코드 블록 중 하나로, 데이터베이스 작업을 수행하기 위해 사용된다.**

**프로시저는 PL/SQL에서 구현된 코드의 일부분을 재사용하고자 할 때 사용되며, 호출되어 실행될 수 있다.**

####   
**종류**

**Procedure : 리턴값을 하나 이상 가질 수 있는 프로그램**  
**Function  : 하나 이상의 프로시저, 함수, 변수, 예외 등의 묶음**  
**Trigger : 지정된 이벤트가 발생하면 자동으로 실행되는 PL/SQL 블록**

**PL/SQL은 원래 단일행에 대한 처리만 가능하지만, CURSOR를 이용하면 다수의 행을 처리할 수 있다.**  
**그래서 마이바티스를 사용하지 않을 때 루프를 돌려 레코드셋을 얻을 때 Cursor를 사용할 수 있는 것이다.**

* * *

```sql
JDBCtype = CURSO
```

**SI를 하면서 Mapper에서 이와 같은 속성을 봤다.**

**JDBCType의 CURSOR는 데이터베이스에서 결과 집합을 가리키는 데이터 타입을 나타내며,**   
**JDBC를 사용하여 데이터베이스로부터 커서 기반의 결과를 가져올 때 사용된다.**

* * *

### **Cursor**

**쿼리문에 의해서 반환되는 결괏값을 저장하는 메모리공간을 가리키는 포인터**

#### **일반 Cursor**

**명시적(Explicit) 커서 : 사용자가 선언해서 생성 후 사용하는 SQL 커서**  
**묵시적(Implicit) 커서 : 오라클에서 자동으로 선언해 주는 SQL 커서**  
  
**명시적/묵시적 커서는 정적이므로, 커서가 만들어지는 시점에 쿼리문이 정의됩니다.**  
**따라서 런타임까지는 커서에 이용할 쿼리를 정의할 수 없기 때문에, 오라클에서는 REF CURSOR와 커서변수를 제공하여**  
**런타임시에 커서의 쿼리를 정의하고 결과 셋을 처리할 수 있도록 하고 있습니다.**

#### **동적 Cursor - REF CURSOR**

**1. 강한 타입(제한적 REF CURSOR) : 쿼리의 결과 셋을 구성할 리턴 타입 선언을 필요**

```sql
TYPE T_CURSOR IS REF CURSOR;
```

  
**2. 약한 타입(비 제한적 REF CURSOR) : 리턴타입 정의를 포함하지 않고, 어떤 결과 셋을 얻는 경우에도 이용 가능**

```sql
TYPE T_CURSOR IS REF CURSOR;
	RETURN A;
```

**참고 블로그 : [https://blog.naver.com/minis24](https://blog.naver.com/minis24)**
