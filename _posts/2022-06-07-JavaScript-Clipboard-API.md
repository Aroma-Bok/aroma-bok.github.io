---
title: "[JavaScript] Clipboard API"
date: 2022-06-07 18:53:02 +0900
categories: ["Language", "JavaScript"]
tags: ["clipboard api", "clipboard api 사용법", "navigator.clipboard"]
tistory_url: "https://aroma-bok.tistory.com/entry/JavaScript-Clipboard-API"
---
#### **프로젝트를 하면서, 해당 부분을 클릭하면 내용이 복사되도록 하는 기능을 개발한 적이 있다.** 

#### **우연히 구글링을 하다가, 클립보드 API가 있는 것을 알게 되었다.**

* * *

**Clipboard API : 클립보드에 내용을 복사하고 붙여 넣을 수 있는 비동기 읽기 및 쓰기 작업을 제공.**

**클립 보드 API는 'navigator.clipboard'개체 내에서 사용 가능.**

**이 API는 JavaScript 언어에 새로 추가된 것으로 모든 브라우저, 특히 이전 버전을 지원할 수 있는 것은 아니다.**

**브라우저에서 이 API를 지원하는지 확인하려면 아래 코드를 사용해서 확인 가능하다.**

```html
if (!navigator.clipboard) {
      console.log("나오면 작동하지 않습니다.");
  }else{
    console.log("나오면 작동합니다.")
  }
```

**사용법은 아래처럼 작성하여 사용이 가능하다.**

```html
<div ><a class="btn-type01 radius" onclick="copyToClickBoard();"><em id="textAddress">${storeInfo.address}</em></a></div>

  function copyToClickBoard(){
      var content = document.getElementById('textAddress').innerHTML;

      navigator.clipboard.writeText(content)
          .then(() => {
          console.log(content);
      })
          .catch(err => {
          console.log('Something went wrong', err);
      })

  }
```
