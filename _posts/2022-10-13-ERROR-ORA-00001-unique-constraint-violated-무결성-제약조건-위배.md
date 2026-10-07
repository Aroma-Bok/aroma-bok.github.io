---
title: "[ERROR] ORA-00001: unique constraint () violated - 무결성 제약조건 위배"
date: 2022-10-13 18:38:32 +0900
categories: []
tags: ["ora-00001: unique constraint () violated"]
tistory_url: "https://aroma-bok.tistory.com/entry/ERROR-ORA-00001-unique-constraint-violated-%EB%AC%B4%EA%B2%B0%EC%84%B1-%EC%A0%9C%EC%95%BD%EC%A1%B0%EA%B1%B4-%EC%9C%84%EB%B0%B0"
---
**원인 : DB에 저장된 PK 값과 동일한 값으로 저장을 시도할 때, 발생**

**해결 : 기존에 등록되어있던 PK값을 삭제하거나, 새로 등록되는 값을 변경**
