---
title: "[ORACLE, TIBERO] DB에 저장된 프로시저, 함수 검색 방법"
date: 2023-06-22 11:05:58 +0900
categories: ["Solution & Tools", "DB"]
tags: ["pms 프로시저 검색", "프로시저 검색법"]
tistory_url: "https://aroma-bok.tistory.com/entry/ORACLE-TIBERO-DB%EC%97%90-%EC%A0%80%EC%9E%A5%EB%90%9C-%ED%94%84%EB%A1%9C%EC%8B%9C%EC%A0%80-%ED%95%A8%EC%88%98-%EA%B2%80%EC%83%89-%EB%B0%A9%EB%B2%95"
---
#### **DB의 PSM (Persistent Stored Modules) 안에 있는 프로시저를 검색하는 방법**

```sql
SELECT * FROM USER_SOURCE;
```

**\-> 프로시저, 함수 등의 소스가 있는 테이블로서 프로시저가 어디서 사용되고 있는지 확인할 때 용이하다.**

```sql
SELECT * FROM USER_OBJECTS;
```

**\-> 테이블, 프로시저, 함수 등 정보가 담겨있는 테이블**

**해당 검색법을 모르면 패키지나, 프리시저를 하나하나 찾아봐야 한다...**
