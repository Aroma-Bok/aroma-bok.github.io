---
title: "[ERROR] com.google.common.util.concurrent.UncheckedExecutionException: java.lang.IllegalArgumentException: Malformed \\uxxxx encoding."
date: 2022-05-17 13:26:09 +0900
categories: ["ETC", "Error"]
tags: ["com.google.common.util.concurrent.uncheckedexecutionexception: java.lang.illegalargumentexception: malformed \\uxxxx encoding.", "uncheckedexecutionexception"]
tistory_url: "https://aroma-bok.tistory.com/entry/ERROR-comgooglecommonutilconcurrentUncheckedExecutionException-javalangIllegalArgumentException-Malformed-uxxxx-encoding"
---
**가끔 Maven 업데이트를 하려고 하면, 아래의 에러가 발생하는 경우가 있다.**

**co[m.google.common.util.concurrent.UncheckedExecutionException:](http://m.google.common.util.concurrent.UncheckedExecutionException:) java.lang.IllegalArgumentException: Malformed \\uxxxx encoding.**

**1.. m2 폴더에 들어간다**

**2\. repository 삭제**

**3\. maven 다시 업데이트하면 잘 된다.**

**\* 기존 repository랑 충돌이 일어나는 건가보다.**
