---
title: "[JavaScript] Web 개발을 위한 JavaScript (7)  - Object +  Literal and properties + Object 생성법 + Computed properties + Property value shorthand +Constructor + in operator + for in vs for of + Object.assign"
date: 2021-12-28 22:25:54 +0900
categories: ["Language", "JavaScript"]
tags: ["for in", "for of", "object", "object 사용법", "object 생성법", "object.assign", "property value shorthand"]
tistory_url: "https://aroma-bok.tistory.com/entry/JavaScript-Web-%EA%B0%9C%EB%B0%9C%EC%9D%84-%EC%9C%84%ED%95%9C-JavaScript-7-Object-Literal-and-properties-Object-%EC%83%9D%EC%84%B1%EB%B2%95-Computed-properties-Property-value-shorthand-Constructor-in-operator-for-in-vs-for-of-Objectassign"
---
### **Object +  Literal and properties + Object 생성법 + Computed properties + Property value shorthand +Constructor + in operator + for in vs for of + Object.assign**

* * *

#### **Object**

**1\. one of the JavaScrip's data types.** 

**: 자바스크립트 데이터 타입의 한 종류**

**2\. a collection of related data and/or functionality.**

**: 관련있는 데이터 또는 함수의 모음**

**3\. Nearly all objects in JavaScript ars instances of Object.**

**: 자바스크립트에 있는 거의 모든 객체는 객체의 인스턴스이다.**

#### **1\. Literal and properties**

**하나의 'primitive type'은 하나의 데이터만 담을 수 있다.**

**아래처럼 2개의 변수를 출력하는 함수를 만든다면, 2개의 파라미터를 전달해야 한다.**

```javascript
const name = 'ellie';
const age = 4;

print(name, age);
function print(name, age){
    console.log(`name : ${name} , age : ${age}`);
}
```

![](/assets/img/posts/52/1.png)

**하지만, 이렇게 작성하면 문제는 무엇일까?**

**인자가 많아지면 많아질수록 추가해야 하는 것들이 많아진다. 그리고 관리도 힘들어진다.**

**그래서 이것을 개선하고자 'Object'를 사용한다.**

```javascript
const ellie = {name: 'ellie', age:4};
print2(ellie);
function print2(person){
    console.log(`Name : ${person.name}, Age : ${person.age}`);
}
```

![](/assets/img/posts/52/2.png)

#### **Object를 만드는 방법**

**1) 퀄리 브래킷{} 사용 : object literal**

**2) 클래스 이용 : object constructor** 

```javascript
const obj = {};
const obj2 = new Object();
```

  

**'JavaScript'는 'Dynamic Type language'이라서 동적으로 Runtime때 'Type'이 결정 된다.**

  

**그래서 이런 '미친짓'을 할 수 있다. 이미 name과 age 'property'를 선언했지만 'property'를 추가할 수 있다.**

```javascript
ellie.hasJob = true; // 새로운 'Property' 추가, 
console.log(ellie.hasJob);
```

![](/assets/img/posts/52/3.png)

**그리고 당연히 삭제도 가능하다.**

```javascript
delete ellie.hasJob;
console.log(ellie.hasJob);
```

![](/assets/img/posts/52/4.png)

**※ 중요 Point!**

**'Object'는 'key'와 'value'의 집합체이다. => object = { key : value }**

**key : 우리가 접근할 수 있는 변수, Property**

**value : 'key'가 가지고 있는 값**

#### **2\. Computed properties : 계산된 Peroperties**

**접근 방법**

**1) '.' 사용**

**2) 배열처럼 접근**

```javascript
console.log(ellie.name); 
console.log(ellie['name']);
```

![](/assets/img/posts/52/5.png)

**주의사항 : 배열처럼 접근할 때는, 'Key : Property'에 'String'으로 접근해야 한다.**

**아래처럼 접근하면 에러가 발생한다. 꼭 ' '을 붙여서 사용하자.**

```javascript
console.log(ellie[name]);
```

![](/assets/img/posts/52/6.png)

**배열처럼 사용해도 'property'를 추가할 수 있다.**

```javascript
ellie['hasJob'] = true;
console.log(ellie.hasJob);
```

![](/assets/img/posts/52/7.png)

**★☆그럼 어떤 걸 언제 써야 할까?!★☆**

**1) '.'  : 코딩하는 그 순간 'Key'에 해당하는 값을 가지고 올 때 사용**

**2) \['Key'\] : 코딩할 때, 어떤 'Key'가 필요한지 모를 때 사용**

**2번에 대해서 자세하게 설명하면 이렇다.**

**코딩하는 순간에는 'key'값이 뭐가 들어올지 모른다. 어떤 'key'가 필요한지 모르기 때문에, 배열처럼 사용해야 한다.**

```javascript
function printValue(obj, key){
   //console.log(obj.key); // 이렇게 사용하면 'undefined'가 뜬다
   console.log(obj[key]) 
}
printValue(ellie, 'name');
```

![](/assets/img/posts/52/8.png)

**console.log(obj.key);처럼 사용한다면, 'obj'에 'key'라는 'property'가 있는 확인하는 것이다.**

**당연히 'key'라는 'property'는 존재하지 않으니까 '결과'는 'Undefined'가 나온다.**

**나중에 동적으로 key에 관련된 'value'를 받아올 때 유용하게 사용된다.**

#### **3\. Property value shorthand**

**만약, 아래처럼 객체를 추가하고자 할 때, 또 계속 작성을 반복을 해야 할까?**

```javascript
const person1 = {name : 'bob', age:2};
const person2 = {name : 'steve', age:3};
const person3 = {name : 'dave', age:4};
```

**기존에 배웠던 함수를 이용한다면 쉽게 만들 수 있다.**

```javascript
function makePerson(name, age){
    return{
        name : name,
        age : age,
    };
}
```

**여기서 'JavaScript'에서는 'Property value shorthand'라는 특징 때문에,**

**'key'값과 'value'가 동일하다면, 1개는 생략이 가능하다.**

```javascript
function makePerson(name, age){
    return{
        name,
        age,
    };
}
const person4 = makePerson('ellie', 30);
console.log(person4);
```

![](/assets/img/posts/52/9.png)

#### **4\. Constructor function**

**'JavaScript'에서 '계산'을 하지 않고 순수하게 'Object'만 생성하는 함수들은**

**함수명을 대문자로 시작하는 명사 형태로 만든다. 위에서 작성한 함수를 아래처럼 변경할 수 있다.**

```javascript
function Person(name, age){
    // this = {}; -> 생략된 코드
    this.name = name;
    this.age = age;
    // return을 사용하지않고 'this'를 사용해서 작성한다.
    // return = {}; -> 생략된 코드
 }
const person5 = new Person('Son' , 29);
console.log(person5);
```

![](/assets/img/posts/52/10.png)

#### **5. in operator : property existence check (key in obj)**

**해당하는 'Object'안에 'key'가 있는지 없는지 확인하는 기능이다.**

**존재한다면 'True', 존재하지 않는다면 'Fasle'를 리턴한다.**

```javascript
console.log('name' in ellie );
```

![](/assets/img/posts/52/11.png)

**6\. for...in vs for... of**

**6-1) for (key in obj)** 

  **'ellie'객체가 블록을 돌 때마다 'key'에 'ellie의 key'가 할당이 된다.**

```javascript
for ( key in ellie){
    console.log(key);
}
```

![](/assets/img/posts/52/12.png)

**6-2) for (value of obj)**

**Object'를 쓰는 것이 아니라, '배열','리스트'같은 순차적인 것들을 쓰는 것이다.**

**'array'에 있는 'value'가 순차적으로 할당된다.**

```javascript
const array = [1,2,3,4]; 
for (let i = 0; i<array.length; i++){
    console.log(array[i]);
    // 아주 일반적인 방법
    // 하지만 소스코드가 길어지는 단점이 있다.
}
for(value of array){
    console.log(value);
}
```

![](/assets/img/posts/52/13.png)

#### **7. Fun cloning** 

**객체를 복사하는 방법이다. 아래 코드를 보자.**

**'user'는 'ref'가리키고 있고, 'ref'는 'ellie','20'을 가리키고 있다.**

**'user2'도 동일한 'ref'가 들어가있고 'ref'도 'ellie','20'을 가리키고 있다.**

```javascript
const user = { name: 'ellie', age: 20};
const user2 = user;
```

**그렇다면, 'user2'의 'name' 값을 바꾼다면?? 당연히 'user'의 'name'값도 변경될 것이다.**

```javascript
user2.name = 'coder';
console.log(user);
```

![](/assets/img/posts/52/14.png)

**그렇다면, 이런 방법 말고 'Object'를 복사하는 방법은 없을까?**

**1) Old way : 임시 배열을 만들어서 for문을 사용해 옮겨 넣는 방법**

```javascript
const user3 = {};
for (key in user){
    user3[key] = user[key];
}
console.log(user3);
```

![](/assets/img/posts/52/15.png)

**2) New Way : Object.assign 사용**

```javascript
const user4 = {}; // 비어있는 'Target'을 정의
Object.assign(user4, user);
// Or const user4 = Object.assign({}, user);
console.log(user4);
```

![](/assets/img/posts/52/16.png)

**++ New Way - another example**

**여러 개를 전달하는 경우에는 동일한 'Property'인 경우에는 뒤에 나오는 인자가, 앞에 나오는 인자의 값을 덮어 버린다.**

**fruit1이 fruit2의 'color' 값을 덮어 버렸기 때문에, 'red'가 출력되는 것이다.**

```javascript
// 여러개를 전달 할 경우
const fruit1 = {color : 'red'};
const fruit2 = {color : 'blue', size:'big'};
const mixed = Object.assign({}, fruit2, fruit1);
console.log(mixed.color);
console.log(mixed.size);
```

![](/assets/img/posts/52/17.png)

* * *

```javascript
//새로운 함수를 공부할때는 어떤 'Parameter'를 취하고 'Retrun'은 뭐로하는지 파악하는 습관을 들이자!
    /**
     * Copy the values of all of the enumerable own properties from one or more source objects to a
     * target object. Returns the target object.
     * @param target The target object to copy to.
     * @param source The source object from which to copy properties.
     */
     assign<T, U>(target: T, source: U): T & U;
```

* * *
