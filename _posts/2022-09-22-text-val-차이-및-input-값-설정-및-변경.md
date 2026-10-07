---
title: "text(), val() 차이 및 input 값 설정 및 변경"
date: 2022-09-22 17:58:56 +0900
categories: ["Language", "JavaScript"]
tags: ["text()", "val()", "val() text() 차이", "val() vs text()"]
tistory_url: "https://aroma-bok.tistory.com/entry/text-val-%EC%B0%A8%EC%9D%B4-%EB%B0%8F-input-%EA%B0%92-%EC%84%A4%EC%A0%95-%EB%B0%8F-%EB%B3%80%EA%B2%BD"
---
**개발을 하다가 콜백 함수로 값을 받아와서 값을 설정해야 할 때가 있었다. 그래서 text()로는 값이 설정이 되지 않고 val()을 사용하여 문제를 해결한 경험이 있었다. 그래서 개념을 다시 잡기 위해 정리를 해본다.**

**1\. $(셀렉터). val()**

-   **양식(form)의 값을 가져오거나 값을 설정할 때 사용**
-   **주로 input, textarear에 사용**

```sql
$("#publ_nm").val("3");
```

![](/assets/img/posts/153/1.png)

**2\. $(셀렉터). text()** 

-   **셀렉터 하위에 있는 자식 태그들의 문자열만 출력 및 html을 변경**
-   **input, textarear 이외 나머지에 사용**

```sql
$("#publ_nm").text("3"); // input에 사용
```

![](/assets/img/posts/153/2.png)

```sql
$("#publ_nm_control").text("3"); // div에 사용
```

![](/assets/img/posts/153/3.png)

**값을 설정하기 위해선 val()을 사용해서 값을 설정하자**
