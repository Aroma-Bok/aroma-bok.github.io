---
title: "ORA-01861 : literal does not match format string"
date: 2022-10-07 11:22:51 +0900
categories: ["ETC", "Error"]
tags: ["literal does not match format string", "literal does not match format string 에러", "ora-01861", "to_chjar", "to_date"]
tistory_url: "https://aroma-bok.tistory.com/entry/ORA-01861-literal-does-not-match-format-string"
---
**운영서버에 올리고 나면 한 번쯤은 겪는 문제라고 생각하는 에러다.**

**원인 : TO\_DATE || TO\_CHAR의 함수에 날짜 형식을 잘못 입력하거나, 입력하지 않았을 때 발생**

**DATE -> TO\_CAHR 일 때, 표현하고자 하는 Format을 지정해줘야 한다.**

**VARCHAR -> TO\_DATE  일 때, 꼭 현 VARCHAR 형식의 Format을 지정해주고 DATE로 변경해야 한다.**

**아래는 필자가 작성한 코드다. 아래의 코드로 설명하면 이렇다.**

```sql
CASE
    WHEN CO.MODIFIED_DT <![CDATA[ IS NOT NULL ]]> THEN TO_CHAR(CO.MODIFIED_DT, 'YYYY.MM.DD')
    WHEN CO.MODIFIED_DT <![CDATA[ IS NULL ]]> THEN TO_CHAR(TO_DATE(MC.END_DT, 'YYYY.MM.DD'), 'YYYY.MM.DD')
    ELSE ''
END AS MOD_DATE
```

![](/assets/img/posts/166/1.png)

*CO.MODIFIED\_DT 타입*

![](/assets/img/posts/166/2.png)

*MC.END\_DT 타입*

**MODIFIED\_DT 타입은 'DATE"이고, END\_DT 타입은 VARHCAR 인 것을 확인할 수 있다.**

**DATE타입에서 TO\_CHAR로 변환할 때는, 표현하고자 하는 Format('YYYY.MM.DD')만 작성해주고,**

**VARCHAR에서 TO\_DATE로 변환할대는, 현재 VARCHAR의 Format('YYYY.MM.DD')을 꼭 작성해줘야 한다.**
