---
title: "location.href vs window.open (+ select url 이동)"
date: 2022-06-27 15:24:59 +0900
categories: ["ETC", "IT Knowledge"]
tags: ["location.href", "location.href vs window.open", "location.href vs window.open 차이점", "select url 이동", "window.open"]
tistory_url: "https://aroma-bok.tistory.com/entry/locationhref-vs-windowopen-select-url-%EC%9D%B4%EB%8F%99"
---
**a태그의 href 역할을  option 태그를 이용해서 사용하고 싶어서 구글링을 하다가 문득 location.href vs window.open 차이점이 궁금해서 정리하게 되었다.**

* * *

**location.href**

**기본적으로 흔히 사용하는 'location.href'은 'method'가 아니라 브라우저의 현재 URL 위치를 알려주는 '속성'이다.**  
**속성 값을 변경하면 현재의 페이지가 리디렉션이 된다.**

  
**window.open**

**새 창에서 열 URL을 전달할 수 있는 'method'이다.** 

* * *

**밑에는 select태그를 이용한 URL 이동 방법이다.**

```bash
<select onchange = "if(this.value) location.href=(this.value);"" >
<option value="http://www.Google.com">Google</option>
```
