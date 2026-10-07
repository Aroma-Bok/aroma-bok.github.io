---
title: "[JavaScript] Web 개발을 위한 JavaScript (8) - JOSN + HTTP + AJAX + XHR + XML + HyperText + Markup + Object to Json + Json to Object + JSON.parser + JSON.stringify"
date: 2021-12-29 23:59:55 +0900
categories: ["Language", "JavaScript"]
tags: ["ajax", "json", "json에서 object 변환", "object", "object에서 json 변환", "xhr", "xml"]
tistory_url: "https://aroma-bok.tistory.com/entry/JavaScript-Web-%EA%B0%9C%EB%B0%9C%EC%9D%84-%EC%9C%84%ED%95%9C-JavaScript-8-JOSN-HTTP-AJAX-XHR-XML-HyperText-Markup-Object-to-Json-Json-to-Object-JSONparser-JSONstringify"
---
**JOSN + HTTP + AJAX + XHR + XML + HyperText + Markup + Object to Json + Json to Object + JSON.parser + JSON.stringify**

* * *

**서버 통신의 시작, 'JSON'에 대해서 공부를 해 볼것이다. 어떻게 활용할 수 있는지 알아보자.**

**브라우저 위에서 동작하고 있는 웹 사이트는 '웹 어플리케이션'과 같은 '클라이언트'들이**

**어떻게 서버와 통신할 수 있는지를 정리한 것이 바로 'HTTP (Hypertext Transfer Protocol)' 이다.**

**어떻게 이 'HyperText'를 '클라이언트'와 '서버'가 서로 주고 받을 수 있는지를 규약한 'Protocol'중 하나이다.**

![](/assets/img/posts/54/1.png)

**※'HyperText'는 웹 사이트에서 이용되고 있는 '하이퍼 링크스'만 얘기하는 것이 아니라,**

 **전반적으로 이용되는 '문서', '이미지 파일'등 이런 것들을 포함해서 말한다.**

**'HTTP'를 이용해서 서버에게 데이터를 요청해서 받아올 수 있는 방법으로는 'AJAX'을 사용하였다.**

**AJAX (Asynchronous JavaScript And XML): 웹 페이지에서 동적으로 서버와 데이터를 주고받을 수 있는 기술을 의미**

![](/assets/img/posts/54/2.png)

**대표적인 예로는, 'XHR (XMLHttpReuqest)'라는 객체가 있다. 브라우저에서 제공하는 객체중에 하나이다.**

**이 객체를 사용하면 간단하게 서버에게 데이터를 요청하고 받아올 수 있다.**

더보기

**※ Note**

**최근 브라우저 'API'에 추가된 'fetch()'을 이용하면 간편하게 데이터를 주고받을 수 있다.**

**하지만 무조건 신상이라고해서 함부로 사용할 수 없다라는것을 알아 두어야한다!**

**'IE'에서는 지원이 되지 않기 때문에 이점을 약간 유의해서 사용해야 한다.**

 

**'AJAX'와 'XHR'에서 반복되는 'XML'이 등장하고 있다. 이 'XML'은 바로 'html'과 같은 'Markup 언어'중에 하나이다.**

**( ※ MarkUp 언어 : 태그 등을 이용하여 문서나 데이터의 구조를 명기하는 언어)**

더보기

**Q : 그렇다면, 서버와 데이터를 주고받을때는 'XML'만 가능한 것인가?**

**A : 아니다. 서버와 데이터를 주고 받을 때는 'XML'뿐만 아니라, 굉장히 다양한 파일 포맷을 전달 받을 수 있다.**

    **아시다시피, 요즘은 'JSON'을 많이 사용하고 있다. 그런데 왜 'XML'이라는 이름으로 지어졌을까?**

    **'AJAX'와 'XHR'을 활발히 개발되고 있을 당시, 'M'사에 있는 'OutLook'을 만드는 개발팀이 활발히 참여해서**

    **만들었다고 한다. 당시에 'OutLook'은 서버와 클라이언트가 데이터 전송을 할 때는 'XML'을 사용했었다.**

    **그래서 'XML'을 'HttpRequest'앞에 붙여서 개발을 하게 되었다고 한다.**

**아직까지도 'XHR'을 흔하게 사용한다. 하지만, 'XML'을 사용하면 불필요한 태그들이 너무 많이 들어가서 파일의 사이즈도 커질 뿐만 아니라, 가독성도 좋지 않다. 그래서 요즘에는 'JSON (JavaScript Object Notation)'을  많이 사용하고 있다.**

![](/assets/img/posts/54/3.png)

**'JSON'은 'ECMAScript 3rd (1999)'에서 나온 'Object'에서 큰 영감을 받아서 만들어진 '데이터 포맷'이다.**

**'JSON'도 {key : value}로 이러우져 있다.**

**'JSON'은 브라우저 뿐만 아니라 모바일에서 서버와 데이터를 주고 받을 때 또는 서버와 통신을 하지 않아도 오브젝트를 파일 시스템에 저장할 때도 이 'JSON' 파일 포맷을 사용한다.**

* * *

#### **JSON**

**1\. Simplest data interchange format : 데이터를 주고 받을 떄 쓸 수 있는 가장 간단한 포맷이다.**

**2\. Lightweight text-based structure : 텍스트를 기반으로 하고 가볍다.**

**3\. easy to read : 읽기 쉽다.**

**4\. key-value pairs : 'key'와 'value'로 이루어져 있다.**

**5\. used for serialization and transmission of data between the network and the network connection**

**: 'serialization'을 위해서 사용한다. 그리고 서버와 데이터를 전송하는데 사용된다.**

**\* 여기서 'serialization'은 '직렬화'를 의미하는데, '객체'를 '바이트 스트림'으로 변환 하는 것.**

 **즉, 객체안에 있는 데이터를 스트림에 쓰기위해 '연속적(Serial) Data' 로 변환 하는 것이다.**

**6\. independent programming language and platform. : 프로그래밍 언어나 플랫폼에 상관없이 쓸 수 있다.**

**E.g) '토끼'라는 'Object'를 '서버'에 전송하려고 한다면, {key : value}을 'Sting' 타입으로 변환해 서버에 전달하게 된다.**

**그리고 서버에서 받아올때도, {key : value}을 'Sting' 타입으로 변환해 전송받아와서 '토끼'라는 'Object'로 변환한다.**

#### **공부 포인트**

**1) Serialize : 'Object'를 어떻게 '직렬화(Serialrize)'를 해서 'JSON'으로 변활할지**

**2) Deserialzie : '직렬화된' 'JSON'을 어떻게 'Deserialize'를 해서 'Object'로 다시 변환할지**

#### **1\. Object to JSON - stringify(obj) : Object -> JSON(string)**

**Console을 보면 '싱글 쿼트'가 아니라, '더블 쿼트'가 들어가 있는 것을 볼 수 있는데, 이것이 'JSON'의 규격사항이다.**

**배열처럼 보일 뿐, 'String'이다.**

```javascript
let json = JSON.stringify(true);
console.log(json);

json = JSON.stringify(['apple','banana']);
console.log(json);
```

![](/assets/img/posts/54/4.png)

**객체를 만들어서 시도해보자.** 

**결과를 확인하면, jump()라는 함수는 포함되지 않는 걸 볼 수 있다.**

**함수는 'Object'위에 있는 데이터가 아니기 때문에 함수는** **제외되기 때문이다.**

**그리고 'Symbol'같은 자바스크립트에서 자체로 들어있는 특별한 데이터도 'JSON'에 포함되지 않는다.**

```javascript
const rabbit = {
    name : 'tori',
    color : 'white',
    size : null,
    birthDate : new Date(),
    //symbol : Symbol('id'),
    jump : () => {
        console.log(`${name} can jump!!!`);
    }
}
console.log(JSON.stringify(rabbit));
```

![](/assets/img/posts/54/5.png)

**그리고 '토끼 Object'에서 'name'과 'color'만 'JSON'으로 하고 싶은 경우에는 배열에 'Property' 이름만 전달하면 된다.**

```javascript
console.log(JSON.stringify(rabbit, ["name","color"]));
```

![](/assets/img/posts/54/6.png)

**여기서 'JSON'으로 변환되는 것을 조금 더 통제하고 싶다면 아까 살펴본 '콜백 함수'를 이용하면 된다.**

**결과를 보면, 모든 'key'와 'value'들이 이 '콜백함수'에 전달 되는 것을 확인 할 수 있다.**

**여기서 제일 처음으로 전달되는 '토끼'의 'Object'를 싸고 있는 제일 최상의 것이 전달이 된다.**

```javascript
json = JSON.stringify(rabbit, (key, value) => {
    console.log(`key : ${key}, value : ${value}`);
    return value;
});
console.log(json);
```

![](/assets/img/posts/54/7.png)

**또 여기서 어떤걸 할 수 있을까? 'key'가 'name'이라는 것이 들어오면, 'ellie'라는 'value'로 설정하고**

**아닌 경우에는 오리지날 'value'로 설정할수도 있다. 아래 결과를 보면 이름이 엘리로 변환되는 것을 확인할 수 있다.**

```javascript
json = JSON.stringify(rabbit, (key, value) => {
    console.log(`key : ${key}, value : ${value}`);
    return key === 'name' ? 'ellie' : value;
});
console.log(json);
```

![](/assets/img/posts/54/8.png)

#### **2\. JSON to Object - parse : JSON(string) -> object**

**위에서는 'stringify'라는 'api'를 이용해서 'Object'를 'JSON'으로 변환했었다.**

**이제는 반대로, ' JSON'을 'Object'로 변환해보자.**

```javascript
const obj = JSON.parse(json);
console.log(obj);
```

![](/assets/img/posts/54/9.png)

**한 가지 포인트! 'rabbit'이라는 'Object'는 'jump'라는 메소드가 있었다.** **호출하면 어떤 결과가 일어날까??** 

```javascript
rabbit.jump();
obj.jump();
```

![](/assets/img/posts/54/10.png)

**우리가 변환한 'Object'는 'Serialized가 된 String'으로 만들어진 'JSON'으로부터 'Object'로 만들었기 때문에,**

**함수는 'Serialize'될 때, 포함되지 않았다. 따라서 'JSON'으로 'Object'를 만든 것에는 'jump'라는 메소드가 없다.**

**+++ '토끼'에는 'birthData'라는 'Object'가 있었는데, 이것은 'Date'라는 'Object'이다.**

**그래서 'Date'안에 있는 'API'를 사용할 수 있다.**

```javascript
console.log(rabbit.birthDate.getDate());
```

![](/assets/img/posts/54/11.png)

**반대로 'JSON'으로부터 마든 'Object'에 'birthDate'를 출력해보자.**

```javascript
console.log(obj.birthDate.getDate());
```

![](/assets/img/posts/54/12.png)

**결과는 당연히 에러가 나온다. 'birthDate'는 이미 'serialize'가 진행될 때, 'String'이 되기 때문이다.**

**그래서 다시 'Date'로 변환하고 싶다면, '콜백함수'를 이용해서 변환할 수 있다.**

```javascript
obj = JSON.parse(json, (key, value) => {
    console.log(`key : ${key}, value : ${value}`);
    return key === 'birthDate' ? new Date(value): value;
});
console.log(obj);
```

![](/assets/img/posts/54/13.png)

**그리고 다시 'console.log(obj.birthDate.getDate());'를 출력하면 출력이 되는 것을 볼 수 있다.**

```javascript
console.log(obj.birthDate.getDate());
```

![](/assets/img/posts/54/14.png)
