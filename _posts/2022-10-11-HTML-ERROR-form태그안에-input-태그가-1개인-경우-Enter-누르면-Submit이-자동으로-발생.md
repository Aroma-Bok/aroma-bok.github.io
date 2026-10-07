---
title: "[HTML ERROR] form태그안에 input 태그가 1개인 경우, Enter 누르면 Submit이 자동으로 발생"
date: 2022-10-11 12:24:21 +0900
categories: ["ETC", "Error"]
tags: ["form태그 input 1개", "input 자동 submit"]
tistory_url: "https://aroma-bok.tistory.com/entry/HTML-ERROR-form%ED%83%9C%EA%B7%B8%EC%95%88%EC%97%90-input-%ED%83%9C%EA%B7%B8%EA%B0%80-1%EA%B0%9C%EC%9D%B8-%EA%B2%BD%EC%9A%B0-Enter-%EB%88%84%EB%A5%B4%EB%A9%B4-Submit%EC%9D%B4-%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C-%EB%B0%9C%EC%83%9D"
---
**검색 부분을 개발하다가 Enter를 치면 검색이 완료되고, 다시 리로드가 발생하는 문제가 생겼다.**

**그래서 구글링을 통해서 원인을 찾아서 해결하였다.**

**원인 :  form안에 input 태그가 1개인 경우에 enter를 누르면, submit이 자동으로 발생한다.**  
**그래서 input에 keydown을 걸어두었기 때문에, submit이 2번 발생했다.**

**해결 : form태그 onsubmit에 return false를 추가하여 리로드가 되는 현상을 막았다.**

**<form onsubmit="return false;">**

```javascript
<form onSubmit="return false">
    <div class="search_faq">
        <div class="search_wrap">
            <input type="search" class="form_text type_search " title="검색어" value="">
        </div>
    </div>
</form>
```
