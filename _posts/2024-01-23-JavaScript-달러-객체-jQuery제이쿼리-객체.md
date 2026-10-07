---
title: "[JavaScript] $(달러) 객체?, jQuery(제이쿼리) 객체?"
date: 2024-01-23 20:53:34 +0900
categories: ["Language", "JavaScript"]
tags: ["$ 객체", "javascript $", "javascript $ 객체", "jquery 객체", "달러 객체"]
tistory_url: "https://aroma-bok.tistory.com/entry/JavaScript-%EB%8B%AC%EB%9F%AC-%EA%B0%9D%EC%B2%B4-jQuery%EC%A0%9C%EC%9D%B4%EC%BF%BC%EB%A6%AC-%EA%B0%9D%EC%B2%B4"
---
**프로젝트를 진행하면서, '$a'와 같이 객체를 선언해서 잘 사용하지는 않았다.**

**하지만 해당 제이쿼리 객체를 사용하면 비교적 편리해서 내용을 정리하게 되었다.**

* * *

#### **jQuery 객체?**

-   **$는 jQuery에서 매우 일반적인 사용으로 변수에 저장된 jQuery 객체를 다른 변수와 구별**
-   **jQuery가 아닐 때에도 jQuery를 사용해서 받은 것을 변수에 넣었다는 것을 표시하기 위함도 있다.**
-   **jQuery 객체라는 것을 구분하기 위해서 $를 붙이는 것**

```sql
// 일반적인 변수 선언
var elementId = "myElementId";
var element = document.getElementById(elementId);
element.style.color = "red";

// jQuery를 사용하여 DOM 요소를 선택하고 스타일을 변경
var $element = $("#myElementId");
$element.css("color", "blue");
```

**위의 $element라는 변수에는 jQuery 객체가 저장되어 있다. jQuery를 사용하면 $를 변수명 앞에 사용하여 해당 변수가 jQuery 객체임을 나타낼 수 있다. 이는 코드 리뷰어나 독자에게 빠르게 이해시켜 주는데 도움이 된다.**

**중요한 것은 $를 사용하는 것이 JavaScript 언어 자체의 구문적인 요구사항은 아니라는 것이다.**

**이는 jQuery 라이브러리에서 권장되는 규약 중 하나이다.**
