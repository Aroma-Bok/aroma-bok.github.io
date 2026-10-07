---
title: "[JavaScript] a(Anchor) 태그 onclick, href 차이?"
date: 2022-06-08 12:11:22 +0900
categories: ["Language", "JavaScript"]
tags: ["a태그 onclick href 차이점", "onclick href 차이점"]
tistory_url: "https://aroma-bok.tistory.com/entry/JavaScript-aAnchor-%ED%83%9C%EA%B7%B8-onclick-href-%EC%B0%A8%EC%9D%B4"
---
**프로젝트를 진행하면서, 가끔 앵커 태그(a태그)에 어떤 경우는 'href'에 javaScript 함수가 정의되어 있고, 어떤 경우에는 onClick에 정의되어 있는 것을 본 적이 있다. 그래서 어떤 차이가 있는지 궁금해서 구글링을 해보았다.**  
  
**코드로 보는 게 더 이해가 빠를 거 같다.**

```bash
function checkAnchor(obj){
  console.log($(obj).attr('attr-check'));
}

<a attr-check="onclick:attr-check" href="javascript:checkAnchor(this);"> </a>
<a attr-check="onclick:attr-check" onclick="javascript:checkAnchor(this);"> </a>
```

  
**결과**   
**1) href - undefined**  
**2) onclick - onclick:attr-check**  
  
**위처럼 href로 함수 호출 시, 자기 자신의 object를 찾지 못해 undefined를 표시하게 된다.**  
**하지만, onclick을 사용하면 정상적으로 자기 자신 this가 인식되어 속성을 표시하게 된다.**  
  
**즉, 앵커 태그에서 함수 호출 시, 자기 자신 객체를 전달할 때는 'onclick'을 사용해야 한다.**
