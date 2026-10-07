---
title: "[CSS] prop vs attr 차이점"
date: 2022-06-13 18:52:41 +0900
categories: ["ETC", "IT Knowledge"]
tags: ["attr", "prop", "prop attr 차이점", "prop vs attr"]
tistory_url: "https://aroma-bok.tistory.com/entry/CSS-prop-vs-attr-%EC%B0%A8%EC%9D%B4%EC%A0%90"
---
**보통 jQuery를 통해서 동적인 화면 구성을 하는 경우가 많다. 그런 경우에, element에 특정한 속성을 지정하게 되는 경우가 많다. 이때, 사용되는 함수가 attr() 또는 prop()이다.**  
  
**attr도 prop도 모두 속성 값을 의미하는 attribute와 property를 의미한다. 하지만, 함수가 나누어져 있다는 것은 차이점이 있다는 말이다.**

> **단, 1.6 버전 기준으로는 큰 차이가 있는 듯하다.**  
> **1.6 버전 이전에는 attr()에서 propert와 attribute가 구분되지 않는 형태로 결과값이 제공되었다고 한다.**  
> **1.6 버전부터는 attr() 속성 그 자체의 값을 반환하고 있으며, prop()는 속성 값을 명시적으로 찾아낼 수 있는 방법을 제공한다.**

**무슨 말이냐면, 예를 들어 보자.**  
  
**예를 들어 체크박스가 2개 있다고 하자.**

**\-HTML 영역**

```bash
<input type="checkbox" id="deerTEST" checked="checked"/>deerTEST
<input type="checkbox" id="deerTEST2" />deerTEST2
```

  
**\-jquery 영역**

```bash
$(document).ready(function(){
    alert("attr checked = " + $("#deerTEST").attr("checked") +
          " prop checked = " + $("#deerTEST").prop("checked"));
          
          // 결과 : attr checked = checked | prop checked = true or false
          
     alert("attr checked2 = " + $("#deerTEST2").attr("checked") +
          " prop checked2 = " + $("#deerTEST2").prop("checked"));
          
          // 결과 : attr checked = undefined | prop checked = true or false
});
```

  
**결과**

**1) attr()로 얻은 첫 번째 체크 박스의 checked는 checked로 얻었지만,**  **2번째 체크박스는 checked 속성이 없기 때문에, undefined로 나온다.**

  
**2) prop()로 얻은 값은 가져올 수 있으며, 명시적으로 true혹은 false로 표현해준다.**

> **jquery API에서도 공식적으로 form 요소의 disabled, selected, checked와 같은 속성 값을 확인 또는 변경하는 경우는, prop()를 사용하는 것을 권장하고 있다.**

**결론적으로**

**attr()을 통해서는 element가 가지는 속성 값이나 정보를 조회 (style, src, rowspan 등)하거나 세팅하는 형식의 업무를 하고,** **prop()를 통해새서는 element가 가지는 실제적인 상태(활성화, 체크, 선택 여부)를 제어하는 업무를 하는 것이 좋겠다.**
