---
title: "[ERROR] has not been accessed in this session no CURRVAL available"
date: 2022-03-14 23:46:09 +0900
categories: ["ETC", "Error"]
tags: ["has not been accessed in this session no currval available", "no currval available"]
tistory_url: "https://aroma-bok.tistory.com/entry/ERROR-has-not-been-accessed-in-this-session-no-CURRVAL-available"
---
**시퀀스의 NEXTVAL을 먼저 호출하고, CURRVAL을 호출해야 한다.**

**에러가 발생했다면, NEXTVAL이 같은 세션에서 먼저 사용되어야 한다. 제대로 로직을 타는지 확인하자.**
