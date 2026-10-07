---
title: "[JDBC-5074] Given string does not represent a number in proper format."
date: 2021-12-28 17:50:44 +0900
categories: ["ETC", "Error"]
tags: ["jdbc-5074"]
tistory_url: "https://aroma-bok.tistory.com/entry/JDBC-5074-Given-string-does-not-represent-a-number-in-proper-format"
---
#### **\[JDBC-5074\] Given string does not represent a number in proper format.**

* * *

![](/assets/img/posts/51/1.png)

**원인 : 주어진 'String'은 'number'를 대신할 수 없다.**

**해결 : 'String'의 ' 'type'를 확인하라.**

**필자는 'Number' 타입에 'String'타입을 넣어서 발생하였다.**

**임의로 'Number'타입으로 'input'을 넣을 수 없는 상황이라 쿼리에서 'DECODE' 함수를 사용하 문제를 해결했다.**

**[https://aroma-bok.tistory.com/entry/DECODE-%ED%95%A8%EC%88%98-%EC%82%AC%EC%9A%A9%EB%B2%95](https://aroma-bok.tistory.com/entry/DECODE-%ED%95%A8%EC%88%98-%EC%82%AC%EC%9A%A9%EB%B2%95)**

[DECODE 함수 사용법](https://aroma-bok.tistory.com/entry/DECODE-%ED%95%A8%EC%88%98-%EC%82%AC%EC%9A%A9%EB%B2%95)
