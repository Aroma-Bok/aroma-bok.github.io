---
title: "[JavaScript] onChange vs onKeyUp"
date: 2022-06-13 19:02:38 +0900
categories: ["Language", "JavaScript"]
tags: ["onchange", "onchange onkeyup 차이", "onchange vs onkeyup", "onchange vs onkeyup 차이점", "onkeyup"]
tistory_url: "https://aroma-bok.tistory.com/entry/JavaScript-onChange-vs-onKeyUp"
---
**비밀번호 관련하여 JavaScript 부분을 수정하는 데 있어서 약간 헷갈리는? 부분이 생겨서 찾아보았다.**

**onChange vs onKeyUp**  
**1) onChange : HTML의 요소가 바뀌었을 때**

**즉, Focus가 발생하기 전의 원래 입력값과 비교하여 변화가 일어났을 경우 blur 이벤트 이후에 발생하는 이벤트**

  
**2) onKeyUp  : 값을 입력할 때마다 이벤트 발생**

**즉, input 이벤트 발생 후, value가 업데이트된 이후에 키보드에서 손을 떼면 발생하는 이벤트.**

**(키를 꾹 눌러서 입력을 반복하거나 할 때는 발생하지 않는다.)**

  
**\* keyup, mouseup - jQuery용 / onKeyup, onMouseup - javascript용**

**('on'이 붙어있으면 javaScript용)**
