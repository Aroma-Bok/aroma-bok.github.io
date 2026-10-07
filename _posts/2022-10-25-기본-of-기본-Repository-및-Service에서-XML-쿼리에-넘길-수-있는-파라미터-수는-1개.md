---
title: "[기본 of 기본] Repository 및 Service에서 XML 쿼리에 넘길 수 있는 파라미터 수는 1개"
date: 2022-10-25 19:34:37 +0900
categories: ["ETC", "IT Knowledge"]
tags: ["repository 및 service에서 xml 쿼리를 호출할 때 전달 파라미터 수"]
tistory_url: "https://aroma-bok.tistory.com/entry/%EA%B8%B0%EB%B3%B8-of-%EA%B8%B0%EB%B3%B8-Repository-%EB%B0%8F-Service%EC%97%90%EC%84%9C-XML-%EC%BF%BC%EB%A6%AC%EC%97%90-%EB%84%98%EA%B8%B8-%EC%88%98-%EC%9E%88%EB%8A%94-%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0-%EC%88%98%EB%8A%94-1%EA%B0%9C"
---
개발하면서 흔히 Repository 또는 Service에서 Xml에 있는 쿼리를 호출할 때, 파라미터를 넘겨줄 수 있다. 그런데 거기서 넘겨줄 수 있는 파라미터 개수는 '1'개였다. 이걸 오늘 알았다...  이런 기본적인걸 개발하면서 배우고 있다. 반성한다...

그래서 2개 이상의 파라미터를 전달할 때는 Map, List을 사용해서 던지는 것이다.

그냥 다 Map, List으로 던지길래 아무 생각 없이 던졌던 나를 반성한다...

* * *

#### **Repository 및 Service에서 Xml 쿼리를 호출할 때 전달할 수 있는 파라미터는 '1'개만 가능!!**
