---
title: "[Javascript] window.open vs location.href 차이"
date: 2022-09-27 15:48:42 +0900
categories: ["Language", "JavaScript"]
tags: ["location.href", "location.href vs window.open 차이", "window.open"]
tistory_url: "https://aroma-bok.tistory.com/entry/Javascript-windowopen-vs-locationhref-%EC%B0%A8%EC%9D%B4"
---
**window.location.href = '[http://www.google.com';](http://www.google.com';) // Will take you to Google.**

**: 현재 창에서 이동**

**window.open( '[http://www.google.com');](http://www.google.com';) / / This will open Google in a new window.**

**: 새 창에서 이동**

> **IOS webview, Safari에서는 기본적으로 window.open 이 막혀있다. 내부적으로 풀어줘야 사용할 수 있다고 한다.**
