---
title: "[MyBatis] resultType vs resultMap 차이점"
date: 2022-09-23 18:23:49 +0900
categories: ["Solution & Tools", "MyBatis"]
tags: ["resultmap", "resulttype", "resulttype resultmap 차이점"]
tistory_url: "https://aroma-bok.tistory.com/entry/MyBatis-resultType-vs-resultMap-%EC%B0%A8%EC%9D%B4%EC%A0%90"
---
**SQL 쿼리문을 작성하다 보면, resultType, resultMap과의 차이를 잘 모르고 사용해서 쓰곤 했다.**  
**그래서 이번 기회에 개념을 잡고 싶어서 정리해봤다.**

**resultType : 클래스명 전체 또는 Alias를 입력해야 한다. VO, DTO 등의 객체를 담을 수 있는 클래스의 경로를 명시하여 사용하면 된다.**

```sql
<select id="selectFavoriteStores" resultType="com.core.store.domain.MyStore">
        SELECT MYSTORE_ID
             , USER_ID
             , BIZ_NO
             , CREATED_DT
             , ( SELECT FN_IS_XCPT_STORE_YN( #{bizNo} ) FROM DUAL ) AS XCPT_YN
        FROM   EMCS_MY_STORE
        <where>
            USER_ID = #{userId}
            <if test="bizNo != null">
                AND BIZ_NO = #{bizNo}
            </if>
        </where>
    </select>
```

**resultMap : 개발자가 직접 POJO(VO) 클래스에 매핑하며, 제한 없이 사용 가능하다.**

```sql
<resultMap id="storeRecomMap" type="hashmap">
    <id property="recomId"        column="RECOM_ID" />
    <result property="regionCd" column="RECOM_REGION_CD" />
    <result property="regionNm" column="RECOM_REGION_NM" />
    <result property="storeNm" column="RECOM_STORE_NM" />
    <result property="storeMenu" column="RECOM_STORE_MENU" />
    <result property="area" column="RECOM_AREA" />
    <result property="storeAddr" column="RECOM_STORE_ADDR" />
    <result property="storeLink" column="RECOM_STORE_LINK" />
    <result property="storeLat" column="RECOM_STORE_LAT" />
    <result property="storeLon" column="RECOM_STORE_LON" />
    <result property="distance" column="DISTANCE" />
    <result property="storeCnt" column="STORE_CNT" />
</resultMap>

<select id="selectStoreRecomRegionList" resultMap="storeRecomMap">
    SELECT
        RECOM_REGION_CD, RECOM_REGION_NM, COUNT(*) AS STORE_CNT
    FROM EMCS_STORE_RECOMM
    GROUP BY RECOM_REGION_CD, RECOM_REGION_NM
    ORDER BY RECOM_REGION_CD ASC
</select>
```

**그렇다면, 번거롭게 굳이 resultMap을 사용해서 매핑을 직접 해서 쓰는 이유가 있을까?라는 생각이 들것이다.**  
  
**그 이유는, 재사용성 때문이다.**

**쉽게 말하면, 아래와 같은 VO가 있다고 가정하자.**

```sql
public class MyStore {
    private String mystoreId;
    private int userId;
    private String bizNo;
    private Date createdDt;
    private String xcptYn;
}
```

**그리고 아래처럼 resultType으로 매핑해서 사용한다고 가정해보자.**

```sql
<select id="selectFavoriteStores" resultType="store.domain.MyStore">
    SELECT MYSTORE_ID
         , USER_ID
         , BIZ_NO -- IF,BIZ_NO가 BIZ로 컬럼명이 변경된다면 사용할 수 없다
         , CREATED_DT
         , ( SELECT FN_IS_XCPT_STORE_YN( #{bizNo} ) FROM DUAL ) AS XCPT_YN
    FROM   EMCS_MY_STORE
    <where>
        USER_ID = #{userId}
        <if test="bizNo != null">
            AND BIZ_NO = #{bizNo}
        </if>
    </where>
</select>
```

**위의 쿼리를 실행하면 문제가 없다.** 

**하지만 칼럼명이 BIZ\_NO -> BIZ로 변경되었다고 가정해보자.**  
  
**그렇다면 resultType으로 매핑할 수 있는 VO를 찾지 못한다.**  
  
**이럴 때는 resultMap을 사용해서 직접 매핑해서 사용해주면 된다.** 

**이런 이유로 resultMap을 사용하게 된다. 즉,** **필요에 따라 사용할 수 있다.**
