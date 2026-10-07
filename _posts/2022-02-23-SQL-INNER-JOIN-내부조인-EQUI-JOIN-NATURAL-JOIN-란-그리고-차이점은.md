---
title: "[SQL] INNER JOIN (내부조인), EQUI JOIN , NATURAL JOIN 란? 그리고 차이점은?"
date: 2022-02-23 21:20:07 +0900
categories: ["Solution & Tools", "DB"]
tags: ["equi join", "inner join", "natural join", "조인방법"]
tistory_url: "https://aroma-bok.tistory.com/entry/SQL-INNER-JOIN-%EB%82%B4%EB%B6%80%EC%A1%B0%EC%9D%B8-EQUI-JOIN-NATURAL-JOIN-%EB%9E%80-%EA%B7%B8%EB%A6%AC%EA%B3%A0-%EC%B0%A8%EC%9D%B4%EC%A0%90%EC%9D%80"
---
**INNER JOIN (내부 조인) - INNER JOIN은 둘 이상의 테이블에 존재하는 공통 속성의 값이 같은 것을 결과로 추출.**

### **INNER JOIN 종류**

**1\. EQUI JOIN (동등 조인) - 둘 이상의 테이블에 존재하는 공통 속성의 동등 비교만을 사용하며, 부등호 조인은 동등 조인에 포함되지 않음.**

**※ EQUI JOIN  표현법 :**  
**1) 명시적 표현법 - ON키워드로 어떤 구문을 조인할 것인지 지정**

```sql
		SELECT A.POINT_CD    AS POINT_CD
			 , A.TP_VEND_CD  AS TP_VEND_CD
			 , A.PP_VEND_CD  AS PP_VEND_CD
			 , A.WD_DESC     AS WD_DESC
			 , A.POINT_VEND_KEY AS POINT_VEND_KEY1
			 , A.POINT_VEND_KEY AS POINT_VEND_KEY2
			 , A.CREATE_DT   AS CREATE_DT  
			 , A.CREATE_POS  AS CREATE_POS 
			 , A.CREATE_USER AS CREATE_USER
			 , A.MODIFY_DT   AS MODIFY_DT  
			 , A.MODIFY_POS  AS MODIFY_POS 
			 , A.MODIFY_USER AS MODIFY_USER
			 , B.CUST_CD AS CSUT_CD
		  FROM TABLEA A, TABLEB B
		  WHERE A.POINT_CD = B.POINT_CD
```

**2) 묵시적 표현법 - FROM 구문에서 단순히 , (콤마)를 사용하여 나열**

```sql
		SELECT A.POINT_CD    AS POINT_CD
			 , A.TP_VEND_CD  AS TP_VEND_CD
			 , A.PP_VEND_CD  AS PP_VEND_CD
			 , A.WD_DESC     AS WD_DESC
			 , A.POINT_VEND_KEY AS POINT_VEND_KEY1
			 , A.POINT_VEND_KEY AS POINT_VEND_KEY2
			 , A.CREATE_DT   AS CREATE_DT  
			 , A.CREATE_POS  AS CREATE_POS 
			 , A.CREATE_USER AS CREATE_USER
			 , A.MODIFY_DT   AS MODIFY_DT  
			 , A.MODIFY_POS  AS MODIFY_POS 
			 , A.MODIFY_USER AS MODIFY_USER
			 , B.CUST_CD AS CSUT_CD
		     FROM TABLE A
		     JOIN TABLEB B
		       ON A.POINT_CD = B.POINT_CD;
```

**2\. NATURAL JOIN (자연 조인) - 동등 조인과 거의 유사. 단, 조인 대상 테이블의 모든 칼럼을 비교하여 같은 칼럼명을 가진 대상으로 조인. 그리고 중복된 칼럼은 한 번만 출력.**

**즉, 공통 칼럼이 있으면 공통 칼럼의 값이 다 같은 값을 출력해온다.**

**추가로 Using 조건절, ON 조건절, WHERE 절에 JOIN 조건을 정의할 수 없음.**

* * *

**EQUI JOIN과의 차이점**

**: POINT\_CD가 2번 출력되는 EQUI JOIN과 반대로 NAUTRAL JOIN은 1번만 출력이 된다. => 중복 X**

**: 공통 칼럼들의 값과 다 같지 않기 때문에 출력되지 않는다.** 

```sql
		SELECT *
		  FROM TABLEA A, TABLEB B
		 WHERE A.POINT_CD = B.POINT_CD
```

![](/assets/img/posts/101/1.png)

*조건에 만족하는 값에 대해서 출력*

```sql
		SELECT *
		  FROM TABLEA
		  NATURAL JOIN TABLEB
```

![](/assets/img/posts/101/2.png)

*공통 컬럼들의 값과 다 같지 않기 때문에 출력되지 않는다.*

* * *

**INNER JOIN에 대해서 종류가 몇 가지 더 있지만 여기서는 EQUI JOIN과 NATURAL JOIN에 대해서** **알아보았다.**
