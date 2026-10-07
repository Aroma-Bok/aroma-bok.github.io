---
title: "[SQL] CDATA - Character Data (문자 데이터)"
date: 2022-06-27 15:50:06 +0900
categories: ["Solution & Tools", "MyBatis"]
tags: ["cdata", "character data"]
tistory_url: "https://aroma-bok.tistory.com/entry/SQL-CDATA-Character-Data-%EB%AC%B8%EC%9E%90-%EB%8D%B0%EC%9D%B4%ED%84%B0"
---
**개발을 하다 보면, MyBatis사용 시 쿼리문에 문자열 비교 연산자 혹은 부등호를 처리할 때가 있다.**

  
**그러면 '<'와 같은 기호를 괄호인지 아니면 비교 연산자 인지 확인이 되지 않는다.**  
  

```csharp
<if test="period != null and period != ''">
  AND A >= B
</if>

-- 그래서 이럴때 사용하는 것이 '<![CDATA [] ]>'이다

<if test="period != null and period != ''">
  AND A <![CDATA[ >= ]]> B
</if>
```

  
**즉, '<! \[CDATA \[ (문자열로 인식) \] \]>' 안에 들어가는 문장은 '문자열'로 인식하게 해 준다.**

 **\* 주의 : 동적쿼리를 작성하는 곳에는 사용하지 않아야 한다.**
