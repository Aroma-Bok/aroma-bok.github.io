---
title: "[JavaScript] Web 개발을 위한 JavaScript (2) - 콘솔에 출력, async 와 defer 차이점 + Console API + use strict"
date: 2021-12-20 16:49:42 +0900
categories: ["Language", "JavaScript"]
tags: ["async", "console api", "defer", "javascript", "js", "use strict"]
tistory_url: "https://aroma-bok.tistory.com/entry/JavaScript-Web-%EA%B0%9C%EB%B0%9C%EC%9D%84-%EC%9C%84%ED%95%9C-JavaScript-2-%EC%BD%98%EC%86%94%EC%97%90-%EC%B6%9C%EB%A0%A5-async-%EC%99%80-defer-%EC%B0%A8%EC%9D%B4%EC%A0%90-Console-API-use-strict"
---
### **콘솔에 출력, async와 defer 차이점 + Console API + use strict**

* * *

#### **1\. Hello World! 작성 및 출력**

**아래 파일을 'main.js'로 저장**

```javascript
console.log('Hello World!');
```

#### **2\. CMD를 통한 node실행**

![](/assets/img/posts/45/1.png)

#### **3\. HTML과 연결하여 JavaScript 실행하기 위한 'index.html' 생성 및 결과 확인**

**여기서부터는 VSC만 사용하였다.**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>

    <script src="main.js"></script>

</head>
<body>
    
</body>
</html>
```

![](/assets/img/posts/45/2.png)

*Ctrl + Shift + i 를 누르면 개발자도구 열림!!*

#### **4\. **★☆**async 와 defer 차이점★☆** 

#### **1) head 안에 script가 들어가 있는 경우**

**브라우저가 한줄한 줄 한 줄 분석하며 Parsing 하면서 DOM으로 병합한다. Parsing 중에 script가 보이면 Parsing을 멈추고 Server로부터 필요한 script를 먼저 다운로드하여온다. 그리고 다시 Parsing으로 넘어간다.**

**문제점은? : 만약 script가 용량이 큰 경우라면 다운에 시간이 소모돼서, 사용자가 웹을 실행시키고 보는 데까지 많은 시간이 필요된다.** **그래서 head에 script를 넣는 방법은 좋은 방법이 아니라고 한다.**

![](/assets/img/posts/45/3.png)

#### **2) body가 끝나기 전에 script가 들어가 있는 경우**

**브라우저가 Parsing을 쭉 하고 나서 페이지가 준비된 다음에 script를 다운(fetching) 받고 실행(executing)한다.**

**이런 경우에는 사용자가 JS를 다운로드하기 전에 페이지가 준비되었기 때문에, 페이지를 먼저 볼 수 있다.**

**문제점은? : 사용자가 페이지를 미리 볼 수는 있지만, '동적인 기능'을 제공하기 위한 다운(fetching) 받고 실행(executing)을 위한 시간을 기다려야 하는 단점이 있다. 즉, 'JS'에 의존을 많이 할수록 제대로 된 기능을 제공하지 못하는 페이지만 보여주는 시간이 길어진다는 것이다.**

![](/assets/img/posts/45/4.png)

#### **3) head 안에 script를 사용하면서 'async'를 사용하는 경우**

**'async'은 'boolean 타입'이기 때문에 선언되는 것만으로도 'True'로 설정되기 때문에 사용이 가능하다.**

**Parsing과 다운(fetching)을 동시에 진행하다가, 다운(fetching)이 끝나면 Parsing을 멈추고 실행(executing)을 한다.**

**그리고 나머지 HTML을 Parsing을 하게 된다.**

**문제점은? : Parsing과 다운(fetching)을 병렬적으로 진행하기 때문에 body밑에 두는 방법보다 다운(fetching)하는 시간을 절약할 수 있는 '장점'이 있다. 하지만, 다운(fetching)이 Parsing보다 빠르기 때문에 필요한 요소가 정의가 되어있지 않은 경우가 있을 수 있다는 위험이 있다.**

![](/assets/img/posts/45/5.png)

*async 사용*

#### **4) head 안에 script를 사용하면서 'defer'를 사용하는 경우**

**Parsing 하다가 script를 만나면 '다운(fetching)만' 하자라는 명령을 내려놓고 Parsing을 계속 진행한다.**

**그리고 Parsing을 다 끝내고 나서 마지막에 실행(executing)을 한다. 딱 봐도 가장 좋은 옵션이다.**

![](/assets/img/posts/45/6.png)

* * *

#### **★참고☆**

**만약 js에 대해서 순서에 의존하는 경우라면, 당연히 a->b->c 순서대로 실행이 되어야 하지만, async를 사용하면 먼저 다운로드하여진 script를 사용하기 때문에, 문제가 생길 수 있다.**

![](/assets/img/posts/45/7.png)

**반대로 defer를 사용한다면, Parsing이 끝나고 다운받아진 script를 실행하기 때문에, 순서대로 실행될 수 있다.**

![](/assets/img/posts/45/8.png)

* * *

#### **use strict?**

**순수 바닐라스크립트를 사용할 때는 'use strict'를 명시해두고 사용하는 것이 좋다고 한다.**

**ECMAScript 5에 정의된 기준 중 하나이다. JavaScript를 생성했을 당시, 빠른 시간 내에 만들어야 했던 언어이기에 굉장히 'flexible'한 언어이다. Java를 배우고 JavaScript를 공부한다면 되게 자유롭다는 느낌을 받을 수 있다.**

**하지만 'flexible === dangerous' 하다고 보면 된다. 그래서 이러한 'flexible'을 잡아주기 위해서 'use strict'를 사용한다.**

**아래를 보면 조금 이해에 도움이 될 것이다.**

![](/assets/img/posts/45/9.png)![](/assets/img/posts/45/10.png)

*변수 선언없이 a에 값을 대입했지만 console에는 잘 찍힌다... 하지만 이건 dangerous!!!!!*

![](/assets/img/posts/45/11.png)![](/assets/img/posts/45/12.png)

*use strict를 사용하면 변수 선언없이 사용한 값을 console에 찍으면 당연히 에러 발생!!*

**즉, 상식적인 범위 안에서 움직일 수 있도록 도와주는 모드!!!**

* * *

#### **※ Console API**

**Console 객체는 브라우저의 디버깅 콘솔에 접근할 수 있는 메서드를 제공합니다.**

**동작 방식은 다르지만, 사실상 표준으로 여겨지는 기능도 여럿 있습니다.**

**(※ node.js & 웹에서도 이용 가능한 이유는 위처럼, Cosole API를 사용하고 있기 때문이다. )**

**어떤 메서드들이 있는지 파악하면 도움이 될 것 같다.**

**[https://developer.mozilla.org/ko/docs/Web/API/Console](https://developer.mozilla.org/ko/docs/Web/API/Console)**

[console - Web API | MDN](https://developer.mozilla.org/ko/docs/Web/API/Console)

**※ JavaScript 공부를 위한 사이트 - [https://developer.mozilla.org/ko/](https://developer.mozilla.org/ko/)**

**: 공식 사이트는 아니지만 많은 개발자들이 공식 사이트처럼 이용하는 페이지라고 한다. 여기에서 JavaScript에 대해 공부하는 것을 추천한다고 한다.**

[MDN Web Docs](https://developer.mozilla.org/ko/)

출처 : https://www.youtube.com/watch?v=tJieVCgGzhs
