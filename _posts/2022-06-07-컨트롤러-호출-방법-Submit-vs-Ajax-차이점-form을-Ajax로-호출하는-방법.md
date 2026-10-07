---
title: "[컨트롤러 호출 방법] Submit vs Ajax 차이점? + form을 Ajax로 호출하는 방법"
date: 2022-06-07 10:50:19 +0900
categories: ["ETC", "IT Knowledge"]
tags: ["form ajax호출 방법", "submit ajax 차이", "submit vs ajax 호출 방법", "컨트롤러 동기 비동기 호출"]
tistory_url: "https://aroma-bok.tistory.com/entry/%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EB%B2%95-Submit-vs-Ajax-%EC%B0%A8%EC%9D%B4%EC%A0%90-form%EC%9D%84-Ajax%EB%A1%9C-%ED%98%B8%EC%B6%9C%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95"
---
**프로젝트를 진행하는 동안에 늘 궁금했던 내용이 있다. 바로 Form(Request)를 Submit으로 호출하는 방법과 Ajax로 호출하는 방법의 차이이다.**

* * *

**1) submit : form 전체 데이터를 날려 페이지가 리로드(Reload)된다. 그렇기에 페이지가 변경되는 경우에 자주 사용한다. 즉, 동기식 (Synchronous) 방식**

**흔히 사용할 때는, 로그인 후 페이지를 이동할 때, submit을 사용한다.**

**(동기방식으로 다른 작업을 하지 못한다.)**

**2) Ajax : 비동기식(Asynchronous) 방식.** 

**\* 비동기식 방식 : 서버에서 return data가 날아오지 않아도 기다리지 않고 다른 작업을 바로 진행하는 방식.**

**그렇기에 대기시간이 줄어들어 웹 페이지를 역동성 있게 표현할 수 있다. (시간 단축)**

**더 자세하게 내용을 알아보면 많은 차이들이 있을 거 같지만, 단순하게 페이지가 리로드(Reload)가 되는 경우에는 'Submit(동기)'방식을 사용하고, 리로드(Reload)가 필요하지 않은 경우에는 'Ajax(비동기)'방식을 사용해서 알맞게 사용하면 되는 것 같다.**

* * *

**추가로, form을 Ajax로 전송하는 방법은 아래와 같다.**

**'form'을 'serialize()'를 시켜주고 그 데이터를 Ajax를 호출할 때, data로 넣어주면 된다.**

```html
function sendData() {

  var form1 = $('formid').serialize();

  $.ajax({
        url : '/~',
        type: 'POST',
        data: form1,
        dataType : 'json',

        success : function(data){
          alert("success");
        },

        error : function(){
          alert("fail");
        }
      });
}
```
