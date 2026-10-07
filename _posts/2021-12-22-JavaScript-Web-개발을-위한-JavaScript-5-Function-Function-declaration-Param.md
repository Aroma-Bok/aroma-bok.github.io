---
title: "[JavaScript] Web 개발을 위한 JavaScript (5)  - Function + Function declaration + Parameters + Default parameters + Rest parameters + Local scope + Return a value + Early return, early exit + Function expression + Callback function + anonymous funct.."
date: 2021-12-22 23:56:25 +0900
categories: ["Language", "JavaScript"]
tags: ["anonymous function", "arrow function", "early exit", "early return", "function declaration", "function expression", "iife", "local scope - 지역변수", "parameters", "rest parameters"]
tistory_url: "https://aroma-bok.tistory.com/entry/JavaScript-Web-%EA%B0%9C%EB%B0%9C%EC%9D%84-%EC%9C%84%ED%95%9C-JavaScript-5-Function-Function-declaration-Parameters-Default-parameters-Rest-parameters-Local-scope-Return-a-value-Early-return-early-exit-Function-expression-Callback-function-anonymous-function-named-function-Arrow-function"
---
### **Function + Function declaration + Parameters + Default parameters + Rest parameters + Local scope + Return a value + Early return, early exit + Function expression + Callback function + anonymous function + named function + Arrow function**

* * *

**'procedural language'는 'Function'이 중요한 역할을 한다. 때로는 'sub-program'이라 불린다.**

**program안에서 작은 program역할을 하기 때문이다.**

#### **Function : Input을 받아서 Output을 하는 것.** 

**1\. Function은 프로그램을 구성하는 기본적인 빌딩 블록이다.**

**2\. Sub-Program**

**3\. 재사용이 가능**

**++ 그리고 Function은 이름을 보고 어떤 기능을 하는지 판단하기 때문에, 'Naming'이 중요하다.**

**Function이 'Object'로 간주되기 때문에, 변수에 할당을 할 수 있고,**

**파라미터로 전달이 될 수 있고, function으로 return 할 수 있다.**

#### **1\. Function declaration - 함수 선언**

**하나의 함수는 하나의 역할만 해야 좋은 함수이다. 그리고 동사 형태로 이름을 짓는 게 좋다.**

**+++'JavaScript'는 선언하는 타입이 없기 때문에, 타입이 필요한 경우에는 난해한 경우가 발생할 수 있다.**

**그래서 나중에는 'TypeScript'를 배워보자.**

```javascript
// 1. Function declaration
// function name{param1, param2} { body(비즈니스 로직)~ return;}
// one function === one thing
// naming : doSomething, command, verb
// e·g creaateCardAndPoint -> createCard, createPoint
// function is object in JS
function printHello(){
    console.log('Hello');
}
printHello();

function log(message){
    console.log(message);
}
log('Hello!!');
log(1234);
```

![](/assets/img/posts/48/1.png)

* * *

**TypeScript를 맛보고 싶으면 아래에 들어가서 'Playground'에 들어가면 활용할 수 있다.**

**좌측에 'TypeScript'를 작성하면 우측에는 'JavaScript'로 변환해준다.**

**방금 배운 내용을 작성해보면 'TypeScript'에는 빨간색으로 오류를 표시하고 있다.**

**바로 'Type'이 지정되어있지 않기 때문이다.** 

**'TypeScript'는 항상 'Parameter' 그리고 'Return'은 'Type'을 지정해 주어야 한다.**

![](/assets/img/posts/48/2.png)

*Type이 없는 상태*

![](/assets/img/posts/48/3.png)

*Type을 지정한 상태*

**'TypeScript'를 사용하면 개발하는 데 있어서 조금 더 명확하게 도움을 줄 수 있다.**

**Cause : 함수의 'Interface'만 봐도 'Parameter', 'return'타입만 봐도 어떠한 기능을 하는지 파악하기 쉽다고 한다.**

**나중에 'JavaScript'를 배우고 꼭 'TypeScript'를 배우도록 하자!**

* * *

**[https://www.typescriptlang.org/](https://www.typescriptlang.org/)**

[JavaScript With Syntax For Types.](https://www.typescriptlang.org/)

* * *

* * *

#### **2\. Parameters**

**'premitive parameters'는 메모리에 value 가 저장되어 있기 때문에 value가 전달된다.**

**'Object Parameters'는 메모리에 reference가 저장되어 있기 때문에 reference가 전달된다.**

```javascript
function changeName(Obj){
    Obj.name = 'coder';
}
const ellie = {name : 'ellie'}; 
console.log(ellie);
changeName(ellie);
console.log(ellie);
```

![](/assets/img/posts/48/4.png)

#### **3\. Default parameters (added in ES6)**

**'ES6'에서 새로 추가된 기능이다.**

**2개의 파라미터를 받고 있는데, 1개의 파라미터만 전달되면 나머지 1개의 파라미터는 'unknown'로 표시된다.**

**미리 전달되지 않을 경우를 대비해서 'default' 값을 지정해두는 방법이다.**

```javascript
// 3. Default parameters (added in ES6)
function showMessage(message, from = 'unknown'){
    console.log(`${message} by ${from}`);
}
showMessage('Hi');
```

![](/assets/img/posts/48/5.png)

*결과*

**원래는 아래처럼 전달되지 않을 경우를 대비해서 코드가 작성되어야 했었다.**

**하지만 이제는 새로 도입된 기능으로 번거로움을 줄일 수 있게 됐다.**

```javascript
// 3. Default parameters (added in ES6)
function showMessage(message, from){
    if( from === undefined){
        from = 'unknown';
    }
    console.log(`${message} by ${from}`);
}
showMessage('Hi');
```

![](/assets/img/posts/48/6.png)

*결과*

#### **4\. Rest Parameters**

**'...'을 사용해서, 'Input'을 배열 형태로 전달이 된다.**

**+++ for문 표현방법 3가지를 다 알아둔다면 유용하게 쓸 수 있을 것 같다.**

```javascript
function printAll(...args){

	// for문 표현방법 1
    // for(let i=0; i<args.length; i++){
    //     console.log(args[i]);
    // }

	// for문 표현방법 2
    // for(const arg of args){
    //     console.log(arg);
    // }

	// for문 표현방법 3
    args.forEach((arg) => console.log(arg));
}
printAll('dream1', 'coding1', 'ellie1');
```

![](/assets/img/posts/48/7.png)

#### **5\. Local scope**

**변수의 Scope는 조금 헷갈릴 수 있는 부분이다.**

 **"밖에서는 안이 보이지 않고 안에서만 밖을 볼 수 있다." 이 문장을 기억하면 공부하는데 많이 유용할 것 같다.**

**'{}안'에서 변수를 정의하면 '지역변수'라 한다.**

**'{}안'에서 정의한것을 밖에서 사용할 수 없지만, 밖에서 정의한 함수는 안에서 사용할 수 있다.**

**+++**

**'Closer' : 부모 함수에서 정의된 변수에 자식 함수가 접근할 수 있는 것**

```javascript
// 5. Local scope - 지역변수
let globalMessage = 'global'; // global variable
function printMessage(){
    let message = 'hello';
    console.log(message);
    console.log(globalMessage);
    function printAnother(){
        console.log(message); // Closer
        let childMessage = 'hello';
    }
    console.log(childMessage);
}
printMessage();
```

![](/assets/img/posts/48/8.png)

#### **6\. Return a value**

**++ return 정의되어 있지 않으면, return undefined와 똑같다. 생략이 가능하다.**

```javascript
function sum(a,b){
    return a+b;
}
const result = sum(1,2); // 3
console.log(`result : ${result}`);
console.log(`sum : ${sum(1,2)}`);
```

![](/assets/img/posts/48/9.png)

#### **7\. Early return, early exit**

**'블록{}' 안에서 로직이 많아지면 가독성이 떨어진다.  if와 else를 번갈아서 사용하는 것보다는**

**조건이 맞지 않을때에는 함수를 빨리 return으로 종료하고 그 다음에 필요한 로직을 작성하는 것이 바람직하다.**

```javascript
// bad 
function pgrade(user){
    if(user.point > 10){
        // long upgrade logic...
    }
}

// good
function upgradeUser(user){
    if(user.point <= 10){
        return;
    }
    // long upgrade logic...
}
```

#### **8\. Function expression**

**변수에 바로 할당해서 함수를 사용할 수 있다.**

**'function declaration'과 'function expression'의 가장 큰 차이점은**

**'function expression'은 할당된 다음부터 호출이 가능하다.**

  

**'function declaration'은 Hoisting이 되기 때문에, 선언이 되기전에 호출이 가능하다.**

```javascript
const print = function() { // anonymous function - 함수에 이름이 없는 경우
    console.log('print');
};
print(); // 함수 호출하듯이 호출 가능
```

![](/assets/img/posts/48/10.png)

*function expression*

```javascript
print(); // 함수 호출하듯이 호출 가능
const print = function() { // anonymous function - 함수에 이름이 없는 경우
    console.log('print');
};
```

![](/assets/img/posts/48/11.png)

*function expression*

```javascript
print();
function print() { // anonymous function
    console.log('print');
}
```

![](/assets/img/posts/48/12.png)

*function declaration*

#### **9\. Callback function**

**2개의 콜백함수가 파라미터로 전달되서 해당되는 상황에 맞게 호출되어 사용하는 함수**

```javascript
function randomQuiz(answer, printYes, printNo){
    if(answer === 'love you'){
        printYes();
    } else{
        printNo();
    }
}
// anonymous function
const printYes = function() { //함수 할당
    console.log('Yes!!');
};
// named function
// better deburgging in debugger's stack straces
const printNo = function print(){ // 함수 할당
    console.log('No!!');
};
randomQuiz('wrong',printYes,printNo);
randomQuiz('love you',printYes,printNo);
```

![](/assets/img/posts/48/13.png)

**++ recursions \- 재귀 : 함수 안에서 함수 '자기 자신'을 부르는 것. 피보나치 등 꼭 필요할 때 사용하라!** 

```javascript
// recursions - 재귀 : 함수 안에서 함수 '자기자신'을 부르는 것
// ㄴ 피보나치 등 다른 이용이 필요할 때 사용하라! 
const printNo = function print(){ // 함수 할당
    console.log('No!!');
    print();  // recursions
};
```

![](/assets/img/posts/48/14.png)

*Recursions를 사용해서 계속해서 호출한다*

#### **10\. Arrow function**

**간단하게 1줄로 'function'을 작성해서 사용하는 방법 항상 'anonymous'로 선언되어 사용된다.**

**간단하게 1줄로 사용할 때는 return 없이 사용이 가능하지만, 로직이 길어져야 한다면, {}을 사용하면 된다.  
단, {}을 사용하면 return을 사용해야 한다.**

```javascript
// 변환 전
const simplePrint = function(){
 console.log('simplePrint!!');
};
simplePrint();

// 변환 후
const simplePrint = () => console.log('simplePrint!!');
simplePrint();

const addResult = (a, b) => a + b;
console.log(addResult(1, 5));

// '{}' 블록을 사용하는 경우
const simpleMultiply = (a,b) =>{
    // do something more
    return a*b;
}
console.log(simpleMultiply(1,3));
```

![](/assets/img/posts/48/15.png)

#### **11\. IIFE - Immediately Invoked Function Expression**

**선언과 동시에 함수를 호출해서 사용하는 방법. 요즘은 잘 사용하지 않지만 이런 방법도 있다~ 라는 정도로 알아두자.**

```javascript
(function hello(){
    console.log('IIFE');
})();
```

![](/assets/img/posts/48/16.png)

* * *

**+++ 과제!! +++**

**'command' 알맞게 값을 return 하는 함수를 만들어보자! (정답은 다음 공부에!!)**

```javascript
// Fun quiz time
// function calculate(command, a, b)
// command: (add, substarct, devide, multiply, remainder)
```
