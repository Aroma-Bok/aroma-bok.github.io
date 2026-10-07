---
title: "[Session] localStorage, sessionStorage 사용법과 차이점"
date: 2022-10-07 16:15:13 +0900
categories: ["Solution & Tools", "Web"]
tags: ["localstorage", "localstorage vs sessionstorage 사용법", "localstorage vs sessionstorage 차이점", "sessionstorage"]
tistory_url: "https://aroma-bok.tistory.com/entry/Session-localStorage-sessionStorage-%EC%82%AC%EC%9A%A9%EB%B2%95%EA%B3%BC-%EC%B0%A8%EC%9D%B4%EC%A0%90"
---
**휴면계정에 본인인증이 붙으면서 id값과 pw값을 한 번에 넘겨줄 수 없는 상황이 발생하여, 세션이라는 개념을 이용해, SessionStorage를 사용하여 해결하였다. 그래서 이번 기회에 제대로 알아보려고 정리해보았다.**

* * *

**일반적으로 데이터는 DB에 저장한다. 하지만, 간단하게 브라우저에서 데이터를 저장하는 방법이 있다.**

**브라우저에서 저장소를 제공하는 것이, LocalStorage, SessionStorage이다.**

**HTML5에서 추가된 저장소라고 한다. 데이터 저장 시 'key : value' 쌍으로 저장되고 key를 기반으로 데이터를 조회한다.**

**F12(개발자 도구)를 이용해서 아래처럼 이동하면 LocalStorage, SessionStorage, Cookies까지 확인할 수 있다.**

![](/assets/img/posts/168/1.png)

#### **사용법**

**LocalStorage와 SessionStorage 문법은 동일하다.**

**1\. 저장**

```javascript
 localStorage.setItem("A", "a"); // String으로 저장
 
 localStorage.setItem("B", {B:"b"}); // String외 다른 타입으로 저장
 
 localStorage.setItem("B", JSON.stringify({B:"b"})); // JSON 메소드 사용하여 저장
```

**Value의 값은 String으로 저장 가능하다. String이 아닌 다른 타입으로 저장 시에는 정상적으로 저장되지 않는다.**

**String 외 다른 타입을 저장하기 위해서는 일반적으로 JSON.stringify()를 사용한다.**

**2\. 가져오기**

```javascript
console.log( localStorage.getItem("A"));  // a출력

console.log( localStorage.getItem("B"));  // [object Object] 출력

console.log( localStorage.getItem("B"));  // {"B":"b"} 출력 - 단순 String

console.log( JSON.parse(localStorage.getItem("B")));   // {B: 'b'} 출력
```

**저장한 Key값으로 Value를 가져온다. String타입 외 다른 타입일 경우에는 JSON.stringify()을 사용해 String화 시킨 데이터를 JSON.parse()를 사용하여 가져와야 한다. JSON.parse()을 사용하지 않고, 불러올 경우에는 그냥 String으로 가져오기 때문에 원하는 방식으로 사용할 수 없다.**

![](/assets/img/posts/168/2.png)

*JSON.parse 를 사용해야 원하는 타입으로 가져올 수 있다!*

**3\. 삭제**

```javascript
sessionStorage.removeItem("A"); // A삭제

sessionStorage.clear(); // 남아있는 모든 데이터 삭제
```

**특정 데이터를 삭제하는 방법과 모든 데이터를 삭제하는 방법 2가지가 있다.**

**특정 데이터를 삭제하는 방법 :  localStorage.removeItem("key")를 사용하여 삭제한다.**

**모든 데이터를 삭제하는 방법 :  localStorage.clear()를 사용한다**

**![](/assets/img/posts/168/3.png)**

![](/assets/img/posts/168/4.png)

**4\. LocalStorage와 SessionStorage 차이점**

**LocalStorage** 

1.  **데이터가 만료되지 않는다 - 브라우저 창을 닫거나 새 로고 침해도 데이터는 유지된다. 직접 삭제 필요**
2.  **브라우저 세션 간에 공유 가능 - 여러 개의 창에 똑같은 데이터가 공유된다**
3.  **서버로 전송되지 않는다**

**SessionStorage** 

1.  **데이터가 세션 단위로 저장된다 - 브라우저 창이 닫히는 순간 데이터는 삭제된다**
2.  **브라우저 세션 간에 공유가 되지 않는다**
3.  **서버에 전송되지 않는다**

**\*\* sessionStorage를 사용하려면, localStorage 자리에 대신 넣어서 사용하면 된다.**
