---
title: "[ERROR] XML fragments parsed from previous mappers does not contain value for"
date: 2022-10-19 16:00:11 +0900
categories: ["ETC", "Error"]
tags: ["xml fragments parsed from previous mappers does not contain value for"]
tistory_url: "https://aroma-bok.tistory.com/entry/ERROR-XML-fragments-parsed-from-previous-mappers-does-not-contain-value-for"
---
**다른 파일의 내용을 include 할 때, 패키 지명까지 써야 한다. 패키지 누락되면 발생되는 에러**

**만약에 아래와 같은 내용의 Common 파일의 sql 내용을 A.xml에서 include를 한다고 가정해보자.**

**Common.xml**

```java
<sql id="pagingHeader-oracle">
        SELECT  *
        FROM  (
        SELECT  a.*, ROWNUM AS rnum
        FROM  (
</sql>
    
<sql id="pagingFooter-oracle">
        ) a
        <![CDATA[
             WHERE  ROWNUM <= #{toData}
            )
     WHERE  rnum > #{fromData}
        ]]>
</sql>
```

**A.xml**

```java
<select id="selectA" resultType="HashMap">
    <include refid="Common.pagingHeader-oracle"/>
     SELECT
           A
       FROM A
       <include refid="Common.sort"/>
       <include refid="Common.pagingFooter-oracle"/>
</select>
```

**이렇게 include 할 때, 패키 지명까지 작성해야 한다.**

**하지만 아래처럼 작성하면 XML fragments parsed from previous mappers does not contain value for 오류가 발생한다.**

```java
<select id="selectA" resultType="HashMap">
    <include refid="pagingHeader-oracle"/>
     SELECT
           A
       FROM A
       <include refid="sort"/>
       <include refid="pagingFooter-oracle"/>
</select>​
```
