---
title: "[ORACLE] 딕셔너리 뷰 USER_TAB_MODIFICATIONS"
date: 2022-11-16 17:34:03 +0900
categories: ["Solution & Tools", "DB"]
tags: ["user_tab_modifications"]
tistory_url: "https://aroma-bok.tistory.com/entry/ORACLE-%EB%94%95%EC%85%94%EB%84%88%EB%A6%AC-%EB%B7%B0-USERTABMODIFICATIONS"
---
**'대용량 데이터 베이스 솔루션' 책을 읽으면서, USER\_TAB\_MODIFICATIONS 사용해서 해당 테이블의 변경내역을 조회할 수 있는 방법을 알아서 이렇게 적어본다.**

* * *

```sql
SELECT   *
  FROM   USER_TAB_MODIFICATIONS       
 WHERE   table_name = 'A' -- 테이블 이름
```

**아래처럼 조회결과를 확인해보면, INSERT, UPDATE, DELETE의 변경 내역을 확인할 수 있다.**

![](/assets/img/posts/176/1.png)
