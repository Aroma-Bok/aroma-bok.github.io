---
title: "[JavaScript] 부분 리로드(새로고침), 원하는 영역만 새로고침 시키기"
date: 2022-10-07 11:28:42 +0900
categories: ["Language", "JavaScript"]
tags: []
tistory_url: "https://aroma-bok.tistory.com/entry/JavaScript-%EB%B6%80%EB%B6%84-%EB%A6%AC%EB%A1%9C%EB%93%9C%EC%83%88%EB%A1%9C%EA%B3%A0%EC%B9%A8-%EC%9B%90%ED%95%98%EB%8A%94-%EC%98%81%EC%97%AD%EB%A7%8C-%EC%83%88%EB%A1%9C%EA%B3%A0%EC%B9%A8-%EC%8B%9C%ED%82%A4%EA%B8%B0"
---
전체 부분을 하기에는 너무 비효율적이고, 내가 원하는 부분만 리로드를 해야 할 때가 있다. 아래처럼 작성하여 문제를 해결하였다.

```javascript
<script>
function refresh(){
      $("#div의 id").load(window.location.href + "#div의 id");
}
</script>
```
