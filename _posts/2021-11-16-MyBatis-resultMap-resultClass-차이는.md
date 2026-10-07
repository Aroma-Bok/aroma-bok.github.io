---
title: "[MyBatis] resultMap? resultClass? 차이는?"
date: 2021-11-16 00:03:03 +0900
categories: ["Solution & Tools", "DB"]
tags: ["db", "ibatis", "mybatis", "parameterclass", "resultclass", "resultmap", "resulttype", "sql"]
tistory_url: "https://aroma-bok.tistory.com/entry/MyBatis-resultMap-resultClass-%EC%B0%A8%EC%9D%B4%EB%8A%94"
---
자바로 개발을 하면서 SQL을 작성하는 XML에서 resultMap, resultClass를 흔하게 볼 수 있었다.

오늘은 2개의 차이점과 개념에 대해서 알아보려고 정리해보았다.

![](/assets/img/posts/15/1.png)

#### 참고 (resultClass -> resultType으로 변경)

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft" data-ke-style="style12"><tbody><tr><td style="width: 50%; text-align: center;"><span style="font-family: 'Noto Serif KR';"><b>iBatis</b></span></td><td style="width: 50%; text-align: center;"><span style="font-family: 'Noto Serif KR';"><b>MyBatis</b></span></td></tr><tr><td style="width: 50%; text-align: center;"><span style="font-family: 'Noto Serif KR';"><b>resultClass</b></span></td><td style="width: 50%; text-align: center;"><span style="font-family: 'Noto Serif KR';"><b>resultType</b></span></td></tr></tbody></table>

* * *

### **resultType :**

-   이 구문에 의해 리턴되는 기대 타입의 패키지 경로를 포함한 클래스명 전체 or 별명(Alias)을 입력
-   매핑하려는 자바 클래스의 전체 경로를 입력
-   VO, DTO 등의 객체를 담을 수 있는 클래스의 경로를 명시
-   자동 매핑이 되지만 일부 제한 있음

```sql
<select id="selectInfo" resultType="com.ex.test.SelectVo">
~
</select>

<select id="selectCount" resultType="int">
~
</select>
```

### **resultMap :** 

-   개발자가 직접 POJO 클래스에 매핑
-   번거롭지만 제한 없이 사용 가능

```sql
<resultMap id="selectMap" resultType="com.ex.test.SelectVo">
	<result property="selectId" column="USER_ID"/>
    <result property="selectName" column="USER_NAME"/>
</resultMap>

<select id="selectInfo" resultMap="selectMap">
~
</select>
```

#### 추가적으로 **Ibatis에서 사용되는 방법**도 알아보았다.

### **parameterClass :**

-   이 구문 실행에 필요한 값을 포함하고 있는 클래스의 타입을 설정
-   기본자료형, ValueObject, Map 타입을 설정할 수 있음
-   조건이 있는 DELETE 구문이라면 작성해야 한다

### **resultClass :** 

-   이 구문 실행 후 조회된 값을 담는 클래스의 타입을 설정
-   SELECT 쿼리일때만 사용 가능
-   기본자료형, ValueObject, Map 타입을 설정할 수 있음
-   Delete문과 같이 return 받을 게 없다면 resultClass는 사용할 필요 없음

```sql
<insert id="insertInfo" parameterClass="com.ex.test.SelectVO" >
~
</insert>

<update id="updateInfo" parameterClass="com.ex.test.SelectVO">
~
</update>

<delete id="deleteInfo" parameterClass="int"> --조건이 있는 삭제
~
</delete>

<select id="selectInfo" resultClass="int">
~
</select>

<select id="selectInfo" parameterClass="string" resultClass="int"> --조건이 있는 조회
~
</select>
```
