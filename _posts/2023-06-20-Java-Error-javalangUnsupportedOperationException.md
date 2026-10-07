---
title: "[Java Error] java.lang.UnsupportedOperationException"
date: 2023-06-20 15:14:25 +0900
categories: ["Language", "Java"]
tags: ["arrats.aslist", "java.lang.unsupportedoperationexception", "unsupportedoperationexception"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-Error-javalangUnsupportedOperationException"
---
### java.lang.UnsupportedOperationException 

Arrats.asList로 사용해서 List를 만들면 원소가 고정되어 있기 때문에 원소를 제거할 수 없다.

```java
List list = Arrays.asList(br.readLine().split(""));
```
