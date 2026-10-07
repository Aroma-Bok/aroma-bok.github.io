---
title: "[Java Error] NoSuchElementException?"
date: 2021-11-17 22:52:43 +0900
categories: ["ETC", "Error"]
tags: ["nosuchelementexception", "런타임에러", "스프링에러", "자바에러", "자바오류"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-Error-NoSuchElementException"
---
**NoSuchElementException**은 더 이상 Element가 없는데도 불러오려고 할 때 발생하는 에러.

즉, 없는 공간의 값을 꺼내려고 할 때 발생.

필자 같은 경우는 Spring기반으로 웹을 만들다가 이 오류를 접하게 되었다.

SQL 쿼리문이 작성되어 있는 곳에서 아래처럼 # 한 개가 누락되어 발생했다.

```sql
<select id="SelectInfo" ParameterClass="Map">
SELECT 
	A
,	B
,	C

FROM TEST

WHERE D = #D

</select>
```

**NoSuchElementException** 에러가 발생했다면 SQL 쿼리문이 작성되어 있는 XML에서 #이 누락되어 있는 건 아닌지 확인해볼 필요가 있다.
