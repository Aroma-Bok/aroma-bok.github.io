---
title: "[JavaScript] Web 개발을 위한 JavaScript (4) - 연산 + String concatenation + Numeric operators + Increment and decrement operators + Assignment operators + Comparison operators + Logical operators + Equality + IF문 (조건문) + 반복문 + 삼.."
date: 2021-12-21 23:44:33 +0900
categories: ["Language", "JavaScript"]
tags: ["javascript", "논리 연산자", "반복문", "비교 연산자", "삼항 연산자", "숫자 연산자", "연산자 종류", "조건문", "증감 연산자", "할당 연산자"]
tistory_url: "https://aroma-bok.tistory.com/entry/JavaScript-Web-%EA%B0%9C%EB%B0%9C%EC%9D%84-%EC%9C%84%ED%95%9C-JavaScript-4-%EC%97%B0%EC%82%B0-String-concatenation-Numeric-operators-Increment-and-decrement-operators-Assignment-operators-Comparison-operators-Logical-operators-Equality-IF%EB%AC%B8-%EC%A1%B0%EA%B1%B4%EB%AC%B8-%EB%B0%98%EB%B3%B5%EB%AC%B8-%EC%82%BC%ED%95%AD-%EC%97%B0%EC%82%B0%EC%9E%90"
---
### **연산 + String concatenation + Numeric operators + Increment and decrement operators + Assignment operators + Comparison operators + Logical operators + Equality + IF문 (조건문) + 반복문 + 삼항 연산자**

* * *

#### **1\. String concatenation - 문자열 접합**

```javascript
// 1. String concatenation
console.log('my' + 'cat');
console.log('1' + 2);
console.log(`string literals : 1+2 = ${1+2}`);
// ${}를 사용하면 안에 있는 변수값을 이용해서 결과를 얻어 온다.
```

![](/assets/img/posts/47/1.png)

*결과*

#### **2\. Numeric operators - 숫자 연산자**

```javascript
// 2. Numeric operators
console.log(1 + 1)  //add
console.log(1 - 1)  //substract
console.log(1 / 1)  //devide
console.log(1 * 1)  //multiply
console.log(5 % 2)  //remainder
console.log(2 ** 3) //exponentiation
```

![](/assets/img/posts/47/2.png)

*결과*

#### **3\. Increment and decrement operators - 증감 연산자**

```javascript
// 3. Increment and decrement operators
let counter = 2;
const preIncrement = ++counter;
// counter = counter + 1; 
// preIncrement = counter;
console.log(`preIncrement : ${preIncrement}, counter : ${counter}`);

const postIncrement = counter++;
// postIncrement = counter;
// counter = counter + 1;
console.log(`postIncrement : ${postIncrement}, counter : ${counter}`);

const preDecrement = --counter; // 4->3
console.log(`preDecrement : ${preDecrement}, counter : ${counter}`);
const postDecrement = counter--;
console.log(`postDecrement : ${postDecrement}, counter : ${counter}`);
```

![](/assets/img/posts/47/3.png)

*결과*

#### **4\. Assignment operators - 할당 연산자**

```javascript
// 4. Assignment operators
let x = 3;
let y = 6;
console.log(x += y); // x = x + y;
x = 3;
y = 6;
console.log(x -= y); // x = x - y;
x = 3;
y = 6;
console.log(x *= y); // x = x * y;
x = 3;
y = 6;
console.log(x /= y); // x = x / y;
```

![](/assets/img/posts/47/4.png)

*결과*

#### **5\. Comparison operators - 비교 연산자**

```javascript
// 5. Comparison operators
console.log(10 < 6);  // less than
console.log(10 <= 6); // less than or equal
console.log(10 > 6);  // grater than
console.log(10 >= 6); // greater than or equal
```

![](/assets/img/posts/47/5.png)

*결과*

#### **6\. Logical operators - 논리 연산자**

#### **6-1) || (OR) 연산자**

![](/assets/img/posts/47/6.png)

![](/assets/img/posts/47/7.png)

**여기서 잠깐!! 중요한 점은! '||(or) 연산자'는 '처음으로 True가 나오면 거기서 멈춘다!'**

**그 값 중 어떤 것이라도 'True'면 'True'를 리턴하기 때문이다.** 

**만약, 'value1' 값이 'True'면, '||(or) 연산자'는 'value1'에서 바로 멈추기 때문에, 'for문'이 실행되지 않는다.**

![](/assets/img/posts/47/8.png)

![](/assets/img/posts/47/9.png)

*wasting이 출력되지 않는다.*

**\* Simple한 Expression일수록 앞에다 배치하는 것이 효율적인 방법이다.**

#### **6-2) && (AND) 연산자**

```javascript
// && (and), finds the first falsy value
// 모든 값이 'True'인 경우 실행
console.log(`and : ${value1 && value2 && check()}`);
```

![](/assets/img/posts/47/10.png)

*결과*

**++ &&(AND) 연산자가 자주 사용되는 방법**

**if(nullableObject != null){  nullableObject.something; }**

**\=> null이 아니면 if문이 실행되도록 하는 코드를**

**nullableObject && nullableObject.something**

**\=> &&(and) 연산자를 사용해 코드를 단순화시켰다.**

#### **6-3) ! (NOT) 연산자**

```javascript
// !(not)
console.log(!value1);
```

![](/assets/img/posts/47/11.png)

*결과*

#### **7\. Equality**

```javascript
// 7. Equality
const stringFive = '5';
const numberFive = 5;

// loose equality - with type conversion
// 타입을 신경쓰지 않고 검사
console.log(stringFive == numberFive);
console.log(stringFive != numberFive);

// === strict equality- no type conversion
// 타입을 신경쓰고 검사 
console.log(stringFive === numberFive);
console.log(stringFive !== numberFive);

// object equality by reference
const ellie1 = {name:'ellie'};
const ellie2 = {name:'ellie'};
const ellie3 = ellie1;
console.log(ellie1 == ellie2);
console.log(ellie1 === ellie2);
console.log(ellie1 === ellie3);
```

![](/assets/img/posts/47/12.png)

*결과*

**++ 'ellie1' 과 'ellie2'는 같은 데이터가 들어있는 'Object'이지만 실제 메모리에는 각각 '다른 reference'가 들어있고,**

**그 'reference'는 서로 다른 'Obejct'를 가리키고 있다.** 

![](/assets/img/posts/47/13.png)

**문제**

```javascript
//equality - puzzler
console.log(0 == false);     // true
console.log(0 === false);    // false? cause : Not Boolean
console.log(''== false);     // true
console.log(''=== false);    // false
console.log(null == undefined);  // true
console.log(null === undefined); //  false
```

![](/assets/img/posts/47/14.png)

*꼭 풀어보자!*

#### **8\. If문**

```javascript
// 8. Conditional operators : if
// if, else if, else
const name = 'ellie';
if(name === 'ellie'){
    console.log('Welcome, Ellie!');
}else if(name === 'coder'){
    console.log('You are amazing coder');
}else {
    console.log('unknown');
}
```

![](/assets/img/posts/47/15.png)

*결과*

#### **9\. Ternary operator - 삼항 연산자**

**True면 value1 값을 return, False면 value2 값을 retrun.**

```javascript
// 9. Ternary operator : ? - 삼항 연산자
// condition ? value1 : value2;
console.log(name === 'ellie' ? 'yes' : 'no');
```

![](/assets/img/posts/47/16.png)

#### **10\. Switch statement**

**else if문이 많이 반복 될 때 사용하면 좋은 방법**

```javascript
// 10. Switch statement
// use for multiple if checks : else if가 반복될 때
// use for enum-like value check : 
// use for multiple type checks in TS
const browser = 'Chrome';
switch (browser){
    case 'IE':
        console.log('go away');
        break;
    case 'Chrome': // 똑같은 리턴이면 묶어서 사용 가능
    case 'FireFox':
        console.log('love you!');
        break;
    default:
        console.log('same all!!!');
        break
}
```

![](/assets/img/posts/47/17.png)

*결과*

#### **11\. Loops - 반복문**

#### **11-1) while**

```javascript
// while loop, while the condition is truthy
// body code is executed.
let i = 3;
while (i>0){
    console.log(`while : ${i}`);
    i--;
}
```

![](/assets/img/posts/47/18.png)

*결과*

#### **11-2) do~while**

**while문과는 다르게, 실행문(body)이 먼저 돌아가고 나중에 조건문이 돌아간다.**

```javascript
// do while - body code is executed first,
// then check the condition.
// 현재 i는 '0' 이다. 일반적인 while면 console에 출력이 안되는게 정상
// 하지만, do~while은 먼저 body code를 실행한다.
do{
    console.log(`do while : ${i}`);
    i--;
} while ( i > 0);
```

![](/assets/img/posts/47/19.png)

*결과*

#### **11-3) for** 

```javascript
// for loop, for(begin; condition; step)
// 미리 선언된 i를 사용하는 경우
for(i=3; i>0; i--){
    console.log(`for : ${i}`);
}

// for문 안에 '지역변수'로 선언해서 할당하여 사용하는 경우
for(let i =3; i>0; i=i-2){
    //inline variable declaration
    console.log(`inline variable for: ${i}`);
}
```

![](/assets/img/posts/47/20.png)

*결과*

**++ for문안에 for문 - 2중 for문**

```javascript
// nested loops
for(let i=0; i<10; i++){
    for(let j=0; j<10; j++){
        console.log(`i: ${i}, j: ${j}`);
    }
}
```

![](/assets/img/posts/47/21.png)

*i : 2, j : 9까지*

**++**

**break - loop를 완전히 끝내는 것**  
**continue - 지금 꺼만 스킵하고 다음 스텝으로 넘어가는 것**

* * *

**연습문제**

```javascript
// Q1. iterate from 0 to 10 and print only even numbers (use continue)

for(let i = 0; i<11; i++){
    if( i%2 !== 0){
        continue; // 짝수가 아니면 i++되면서 다음 스텝으로 넘어간다.
    }
    console.log(`Q1 i : ${i}`);
}
```

```javascript
// Q2. iterate from 0 to 10 and print numbers until reaching (use break)

for(let i = 0; i<11; i++){
    if(i > 8){
        break;
    }
    console.log(`Q2 i : ${i}`);
}
```
