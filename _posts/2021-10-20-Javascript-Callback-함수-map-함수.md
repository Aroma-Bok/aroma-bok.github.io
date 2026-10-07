---
title: "[Javascript] Callback 함수 & map 함수"
date: 2021-10-20 00:14:40 +0900
categories: ["Language", "JavaScript"]
tags: ["callback", "callback function", "callback함수", "javascript", "map", "mapfunction", "map함수"]
tistory_url: "https://aroma-bok.tistory.com/entry/Javascript-Callback-%ED%95%A8%EC%88%98-map-%ED%95%A8%EC%88%98"
---
업무 특성상 Javascript를 많이 접하게 되었다. 학원에서 배웠던 내용으로는 역시나 모르는 부분들이 많았다.

그중에서 가장 많이 보였던 함수는 map이었다. 그래서 오늘은 map 함수를 관련해서 포스팅을 해보려고 한다.

먼저 map 함수를 알아보기 전에 Callback함수에 대해서 알아보자.

### **Callback 함수?**

**: 파라미터로 함수를 전달받아, 함수의 내부에서 실행하는 함수**

```javascript
 function myName(subject, callback) {
        console.log(`안녕 내 이름은 ${subject} 이야.`);
        callback();
    }

    myName('자바스크립트', function() {
        console.log('오늘은 자바스크립트를 공부해보자.');
    });
    
    결과
    안녕 내 이름은 자바스크립트 이야.
    오늘은 자바스크립트를 공부해보자.
```

setTimeout 함수를 사용해서 시간을 임의로 지정해서 함수를 호출하는 방법도 있지만, 이 방법은 단순히 딜레이 시켜서 다른 함수를 호출하는 방법이라고 한다. 

Callback함수를 사용하는 이유는 요청을 보낸 후 응답을 기다려야하는 상황을 대비해서 사용한다고 한다.

즉, 함수를 호출하는 시점이나 동작하는 순서가 조금씩 다르게 한다고 보면된다. 

#### **map 함수?** 

**: callback Function 을 실행한 결과를 가지고 새로운 배열을 만들 때 사용하는 함수**

```javascript
    (function test() {
        var num = [1, 2, 3, 4];
        var returnNum = num.map(function(current, indx, array) {
            console.log(current); //배열 내 현재 값
            console.log(indx); // 배열 내 현재 값의 인덱스
            console.log(array); // 현재 배열
            return current * 2;
        });
        console.log(returnNum);
    })();

1
0
Array(4)
0: 1
1: 2
2: 3
3: 4
length: 4

2
1
Array(4)
0: 1
1: 2
2: 3
3: 4
length: 4

3
2
Array(4)
4
3
Array(4)

Array(4)
0: 2
1: 4
2: 6
3: 8
length: 4

-출처: https://aljjabaegi.tistory.com/315
```

map 함수는 기존의 배열을 callback 함수에 의해 새 배열을 만드는 함수라고 한다.
