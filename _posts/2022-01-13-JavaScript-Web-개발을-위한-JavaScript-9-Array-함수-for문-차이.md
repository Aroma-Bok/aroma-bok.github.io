---
title: "[JavaScript] Web 개발을 위한 JavaScript (9) - Array 함수 + for문 차이"
date: 2022-01-13 23:46:12 +0900
categories: ["Language", "JavaScript"]
tags: ["array api", "array 함수", "includes", "indexof", "lastindexof", "pop", "push", "shift", "splice", "unshift"]
tistory_url: "https://aroma-bok.tistory.com/entry/JavaScript-Web-%EA%B0%9C%EB%B0%9C%EC%9D%84-%EC%9C%84%ED%95%9C-JavaScript-9-Array-%ED%95%A8%EC%88%98-for%EB%AC%B8-%EC%B0%A8%EC%9D%B4"
---
![](/assets/img/posts/67/1.png)

**프로젝트를 진행하다 보면 배열을 사용하지 않은 적이 없다.** **그래서 오늘은 배열 함수 중에서**

**Array를 알아보려고 한다.**

**일상생활에서 비슷한 것을 한 곳에 담아두는 것을 '자료구조'라고 한다.**

**어떤 방식, 어떤 형식으로 'Data'를 담냐에 따라서 다양한 타입들이 있다.**

**비슷한 종류의 데이터를 묶는 게 'Object'라 했는데 차이점은?**

더보기

**Object = 토끼, 당근**

**토끼 => 귀 2개, 먹는다, 뛴다 - Property, Method**

**당근 => 주황색 , 비타민C - Property만 존재**

**즉, 'Object'는 서로 연관된 '특징'과 '행동'들을 묶어 놓는 것을 의미**

**e.g) 토끼, 사람, 물체 등**

**비슷한 타입의 'Objsect'들을 묶어 놓는 게 바로 '자료 구조'라고 한다.**

**보통, 다른 프로그래밍 언어에서는 '동일한' 타입의 '자료구조'끼리만 묶을 수 있지만**

**'JavaScript'는 'dynamic typed language'이기 때문에 다른 '자료구조'도 같이 묶을 수 있다.**

**하지만, 이런 방식은 좋지 않다!!!**

**추가적으로 나중에 더 공부해야 할 부분은, 자료구조에 관련해서 '검색', '삽입', '정렬', '삭제'등 과 같은**

**어떤 알고리즘을 써서 '효율성'을 높일 수 있는지 공부하는 게 좋다!**

  
  

**배열은 'index'가 지정되어 있다. 그리고 '0'부터 시작한다! 삽입, 삭제 등 배열에 접근할 때, 'index'로 접근하자!**

### **1\. Declaration**

```javascript
const arr1 = new Array();
const arr2 = [1,2]; //대괄호
```

### **2\. Index position**

**Console에서 출력되는 proto는 아직 배우지 않은 부분이니까 일단 넘어가자.**

**'객체. length - 1'은 보통, 배열의 마지막 값을 알아보고자 할 때 사용하는 방법.**

```javascript
const fruits = ['🍎', '🍌'];
console.log(fruits);
console.log(fruits.length);
console.log(fruits[0]);
console.log(fruits[1]);
console.log(fruits[3]); // 값이 없기 때문에, 'undefined'
console.log(fruits[fruits.length-1]);
```

![](/assets/img/posts/67/2.png)

**'Object'와의 차이점을 본다면, 'Key'값에 상응하는 'Value'를 가지고 올 때, 'String'으로 입력을 해주었지만**

**'배열'에서는 숫자로 입력하여 받아온다.**

### **3\. Looping over an array**

### **3-1) For**

```javascript
for(let i =0; i<fruits.length; i++){
    console.log('for : ' +fruits[i]);
}
```

![](/assets/img/posts/67/3.png)

### **3-2) For of**

**배열 안에 들어있는 인자를 새로운 변수에 하나씩 담는다. 즉, 이터러블 순회 전용**

```javascript
for(let fruit of fruits){
    console.log('for of :' + fruit);
}
```

![](/assets/img/posts/67/4.png)

더보기

**For in과의 차이점**  
  
**For in : 객체의 프로퍼티 키 열거 전용**  
  
**for(let fruit in fruits){**  
    **console.log('for in :' + fruit);**  
**}**

![](/assets/img/posts/67/5.png)

### **3-3) forEach**

**배열 안에 들어있는 'value'들 마다 내가 전달한 '함수'를 전달한다.**

**forEach 함수는 '콜백 함수'를 받아온다. fruits의 'API'를 이용하는 방법이다.**

![](/assets/img/posts/67/6.png)

```javascript
    /**
     * Performs the specified action for each element in an array.
     * @param callbackfn  A function that accepts up to three arguments. forEach calls the callbackfn function one time for each element in the array.
     * @param thisArg  An object to which the this keyword can refer in the callbackfn function. If thisArg is omitted, undefined is used as the this value.
     */
 forEach(callbackfn: (value: T, index: number, array: T[]) => void, thisArg?: any): void;

'?' 로 되어 있는 부분은 전달해도 되고, 안해도 된다는 의미.
```

### **4\.  Addtion, deletion, copy**

더보기

**참고!**

**shift, unshift are slower than pop, push!!!** 

**배열 뒤에서부터 값을 넣었다가 지웠다가 하는 방법은 기존의 데이터들이 움직이지 않기 때문에,** **뒤에서 생기는 공간만 움직이게 된다.**

**반면에, 배열 앞에다가 데이터를 넣거나 지우려고 한다면, 기존의 데이터들의 위치를 이동시키고 데이터를** 

**넣어주거나 삭제를 해야 하기 때문에 반복이 일어난다.** **배열의 길이가 길면 길수록 더 많은 시간이 요구된다!**

  

**그래서 'pop'과 'push'를 사용하는 것이 더 효율적이고 좋다!**

```javascript
// push: add an item to the end
fruits.push('🍓','🍑');
console.log(fruits);

// pop : remove an item fro the end
fruits.pop();
fruits.pop();
console.log(fruits);

// unshift : add an itemm to the beginning
fruits.unshift('🍓','🍑');
console.log(fruits);

// shift :  remove an item to the beginning
fruits.shift();
fruits.shift();
console.log(fruits);

// splice : remove an item by index position
// 지정된 위치에서 데이터를 삭제 또는 삽입이 가능하다.
fruits.push('🍓','🍒','🍋','🍑');
console.log(fruits);
// fruits.splice(1); // 몇개를 지울껀지 지정하지 않으면, 지정한 위치부터 모든걸 삭제한다.

fruits.splice(1,1); //바나나 삭제
console.log(fruits);

// 그리고, 원하는 위치에 삽입도 가능하다. 
fruits.splice(1,1, '🍖','🍙'); // 딸기 삭제하고, 그 위치에 다른 이모지 추가
console.log('splice 삭제 후 추가 : ' + fruits);
fruits.splice(1,0,'🍟'); // 삭제하지 않고, 그 위치에 삽입도 가능.
console.log('splice 삭제하지 않고 추가 : ' + fruits);

// combine two arrays
// 2가지의 배열을 묶어서 이용할 수 있다.
const fruits2 = ['🍕','🍔'];
const newFruits = fruits.concat(fruits2);
console.log(newFruits);
```

![](/assets/img/posts/67/7.png)

#### **5\. Searching**

**indexOf - find the index, 처음 발견한 'index'를 리턴, 배열에 없는 값은 '-1'을 리턴**

**lastIndexOf - find the index, 가장 마지막에 발견한 'index'를 리턴, 배열에 없는 값은 '-1'을 리턴**

**includes - True, False로 return**

**우리가 배열 안에 어떤 값이 몇 번 인덱스에 있는지 알려고 할 때 유용하다.**

```javascript
// indexOf - find the index
console.log(fruits);
console.log(fruits.indexOf('🍙')); 
console.log(fruits.indexOf('🍕')); 

// includes - True, False로 return
console.log(fruits.includes('🍙')); 
console.log(fruits.includes('🍕'));

// lastIndexOf
fruits.push('🍙');
console.log(fruits);
console.log(fruits.indexOf('🍙'));
console.log(fruits.lastIndexOf('🍙'));
```

![](/assets/img/posts/67/8.png)
