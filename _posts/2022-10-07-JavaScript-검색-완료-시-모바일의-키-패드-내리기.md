---
title: "[JavaScript] 검색 완료 시, 모바일의 키 패드 내리기"
date: 2022-10-07 10:55:50 +0900
categories: ["Language", "JavaScript"]
tags: ["$(this). blur()", "모바일 키패드 내리기"]
tistory_url: "https://aroma-bok.tistory.com/entry/JavaScript-%EA%B2%80%EC%83%89-%EC%99%84%EB%A3%8C-%EC%8B%9C-%EB%AA%A8%EB%B0%94%EC%9D%BC%EC%9D%98-%ED%82%A4-%ED%8C%A8%EB%93%9C-%EB%82%B4%EB%A6%AC%EA%B8%B0"
---
모바일의 키 패드도 개발에서 제어해야 할 줄은 몰랐다. 정말 개발하면서 많이 배운다.

$(this). blur(); 를 사용해서 검색하고 완료 버튼을 누르면 키패드가 내려간다.

```javascript
if (e.keyCode == 13)
{
	e.preventDefault();
	$(this).blur();
	searchStart();
}
```
