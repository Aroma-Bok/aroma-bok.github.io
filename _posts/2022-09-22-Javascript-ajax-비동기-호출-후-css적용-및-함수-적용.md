---
title: "[Javascript] ajax 비동기 호출 후, css적용 및 함수 적용"
date: 2022-09-22 18:44:54 +0900
categories: ["Language", "JavaScript"]
tags: ["done() 함수", "비동기 후 함수 추가"]
tistory_url: "https://aroma-bok.tistory.com/entry/Javascript-ajax-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%98%B8%EC%B6%9C-%ED%9B%84-css%EC%A0%81%EC%9A%A9-%EB%B0%8F-%ED%95%A8%EC%88%98-%EC%A0%81%EC%9A%A9"
---
**ajax로 동적으로 데이터를 불러와서 사용할 때, CSS 관련 기능들이 적용이 안돼서 하루 종일 고생했다.**

**ajax를 JSTL로 바꾸고 나니 동적으로만 가능한 페이지라는 것을 깨닫고 다시 고치고...**  
**그러다가 우연히 구글링을 통해서, 비동기 호출 후, 'done()' 함수를 호출해서 ajax호출이 끝나고 새롭게 호출하려는 함수를 적용하니까 css관련 기능이 너무 잘 먹었다. 기분이 너무 좋았다...**

```sql
 $.ajax({
	url: ~~,
    type: 'GET',
    data: $("#listForm").serialize(),
    dataType: 'json',
	success: function(data, status, xhr) { }, 'json').done(function(){
    // 실행 및 적용시키고자 하는 소스
    });
  })
```

**이런 식으로, success function이 끝나고 '. done()' 함수를 붙여서 추가로 실행 및 적용시키고자 하는 소스를 작성해서 사용하면 된다.**

**아래는 필자의 코드이다. 필자는 Swiper()를 재 실행시키려고 함수를 어떻게 적용해야 하나 고민하다가 아래처럼 사용해서 해결했다.**

```sql
 // 필자의 소스
    
     $.ajax({
	url: ~~,
    type: 'GET',
    data: $("#listForm").serialize(),
    dataType: 'json',
	success: function(data, status, xhr) { }, 'json').done(function(){
            	FnbarcodeSwiper(); });
       		 })
```
