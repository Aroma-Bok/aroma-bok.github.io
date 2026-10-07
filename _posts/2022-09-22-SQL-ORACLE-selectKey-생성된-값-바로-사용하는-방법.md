---
title: "[SQL - ORACLE] selectKey?, 생성된 값 바로 사용하는 방법"
date: 2022-09-22 17:27:23 +0900
categories: ["Solution & Tools", "DB"]
tags: ["selectkey", "생성된 값 바로 사용하는 방법"]
tistory_url: "https://aroma-bok.tistory.com/entry/SQL-ORACLE-selectKey-%EC%83%9D%EC%84%B1%EB%90%9C-%EA%B0%92-%EB%B0%94%EB%A1%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95"
---
**개발을 하다가 A라는 테이블에 Insert를 하고, 연속으로  B라는 테이블에 저장한 A테이블의 값을 사용하여 Insert를 해야 하는 경우가 있다. 이런 문제를 해결하기 위해서 오라클의 selectKey 방법을 알게 되었다.** 

```sql
 <insert id="insertA">
    <selectKey resultType="string" keyProperty="ATCH_FILE_GRP_ID" order="BEFORE">
            SELECT TO_CHAR(SYSDATE, 'YYYYMMDD') || LPAD(A.NEXTVAL, 16, 0) FROM DUAL
    </selectKey>
    	INSERT INTO A
    	(
    		ATCH_FILE_GRP_ID, REG_DTM, REGR_ID, MOD_DTM, MODR_ID
    	)
    	VALUES
    	(
    		#{ATCH_FILE_GRP_ID}
    		, TO_CHAR(SYSDATE, 'YYYYMMDDHH24MMSS'), #{userId}, TO_CHAR(SYSDATE, 'YYYYMMDDHH24MMSS'), #{userId}
    	)
    </insert>
    
    
    
    <insert id="insertB"> 
    	INSERT INTO B
    	(
    		ATCH_FILE_GRP_ID, 
    		SEQ, FILE_SIZE_VAL, 
    		DNLD_CNTR, REG_DTM, REGR_ID, MOD_DTM, MODR_ID,
    		ATCH_FILE_URL_PATH_NM
    	)
    	VALUES
    	(
    		#{ATCH_FILE_GRP_ID}, 
        	#{SEQ}, #{attachSize}, 
    		0, TO_CHAR(SYSDATE, 'YYYYMMDDHH24MMSS'), #{userId}, TO_CHAR(SYSDATE, 'YYYYMMDDHH24MMSS'), #{userId},
    		#{downloadPath}
    	)
    </insert>
```

```sql
'resultType' : 해당 컬럼의 데이터 타입
'keyProperty': 해당 컬럼명
'order' : 실행시킬 타이밍 (before:insert전 || after:insert후)
```

**Insert 구문 안에 selectKey가 들어가 있다. 실제 A테이블에 Insert 하기 전에 selectKey에서 필요한 파라미터를 만들고 들고 있다가 B테이블에 A테이블의 값을 전달해 줄 수 있다.** 

**selectKey는 즉, 생성된 값을 다음에 활용하기 위하여 저장한다는 데에 있다. 물론 쓰는 이유는 시퀀스로 사용하기 위한 방법도 있다. 하지만 필자는 값을 전달해주기 위해서 위 방법을 사용하였다.**
