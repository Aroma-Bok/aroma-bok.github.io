---
title: "[JavaScript] Web 개발을 위한 JavaScript (6) - Class + Getter&Setter(게터&세터) + Fields (public,private) + Static + Inheritance - 상속 + 다형성 + 재정의 (Overridng) + instanceOf"
date: 2021-12-27 21:47:48 +0900
categories: ["Language", "JavaScript"]
tags: ["class", "constructor", "getter", "inheritance", "instanceof", "overriding", "setter", "다형성", "상속", "재정의"]
tistory_url: "https://aroma-bok.tistory.com/entry/JavaScript-Web-%EA%B0%9C%EB%B0%9C%EC%9D%84-%EC%9C%84%ED%95%9C-JavaScript-6-Class-GetterSetter%EA%B2%8C%ED%84%B0%EC%84%B8%ED%84%B0-Fields-publicprivate-Static-Inheritance-%EC%83%81%EC%86%8D-%EB%8B%A4%ED%98%95%EC%84%B1-%EC%9E%AC%EC%A0%95%EC%9D%98-Overridng-instanceOf"
---
**Class + Getter&Setter(게터&세터) + Fields (public, private) + Static + Inheritance - 상속 + 다형성 + 재정의 (Overridng) +** **instanceOf**

* * *

**Class : 조금 더 연관있는 데이터를 모아놓는 '컨테이너' 같은 역할**

```javascript
class person {
     name; //속성
     age:  //속성
     speak(); //행동
}
```

**'클래스'는 '속성(fields)'과 '행동(methods)'이 종합적으로 묶여 있는 것이다. 즉, 관련있는 변수나 함수를 묶어 놓은 것.**

**간혹 '속성(fields)'만 들어있는 클래스도 있는데, 이것을 '데이터 클래스'라고 부른다.**

**그리고 클래스 '내부적으로 보이는 변수'와 '밖에서 보일 수 있는 변수'를 나누기도 하는데,** 

**이것을 '캡슐화 = encapsulation'이라고 부른다.**

**그리고 '클래스'를 통해서 '상속'과 '다형성'이 일어날 수 있는데 이런 모든 것들이 가능한것이 '객체지향 언어' 이다.**

더보기

**Class - 붕어빵 틀**

 **-template**

 **-declare once**

 **-no data in**

**Objeect - 팥붕어빵, 슈크림 붕어빵**

 **-instance of a class**

 **-created many times**

 **-data in** 

####  **1. Class declarations**

**생성자를 통해서 Object를 만들 때, 필요한 data를 전달** 

**새로운 Object를 만들 때는, new를 사용한다.**

```javascript
class Person{
    //constructor
        //fields
        this.name = name; 
        this.age = age;
    }
    //methods
    speak(){
        console.log(`${this.name}:hello!`);
    }
}

const ellie = new Person('ellie', 20);
console.log(ellie.name);
console.log(ellie.age);
ellie.speak();
```

![](/assets/img/posts/49/1.png)

#### **2\. Getter and Setters (게터, 세터)**

```javascript
class User{
    constructor(firstName, lastName, age){
        this.firstName = firstName;
        this.lastName = lastName;
        this.age = age;
    }
     get age(){
         return this._age;
     }

     set age(value){ //파라미터 필요
         if(value<0){
             throw Error('age can not be negative!!');
         }
     }
}
```

![](/assets/img/posts/49/2.png)

 **여기까지만 작성하면, 'call stack'이 초과되었다고 에러가 발생한다.**

#### **집중!**

**Getter를 정의하는 순간, '메모리'에 올라가 있는 'data'를 읽어오는게 아니라,  Getter의 return 값을 호출한다.**

**Setter를 정의하는 순간, '메모리'에 할당하는 것이 아니라,  Setter를 호출하면서 무한정으로 계속 반복하게 된다.**

**그래서 이것을 방지하기 위해서 Getter와 Setter에 쓰이는 '변수'는 이름을 조금 다르게 만들어줘야 한다.**

  

**age-> \_age**

**그래서 User 라는 '클래스' 안에는 3개의 '필드'가 있다.  (firstName, lastName, \_age)**

**.age를 호출하고 할당할 수 있는 이유는 내부적으로 'Getter' & 'Setter'를 사용했기 때문이다.**

**그래서 아래와 같이 코드를 수정 하면 에러가 없이 잘 실행된다.**

```javascript
class User{
    constructor(firstName, lastName, age){
        this.firstName = firstName;
        this.lastName = lastName;
        this.age = age;
    }
     get age(){
         return this._age;
     }
     set age(value){ //파라미터 필요
        this._age = value < 0 ? 0 : value;
     }
 }
```

```javascript
const user1 = new User('Stive' , 'job', -1);
console.log(user1.age);
```

![](/assets/img/posts/49/3.png)

**++ 나이가 '-1'인 경우는 말이 안 되는 경우이다.**   
**그래서 이렇게 사용자의 오류를 잡아주는 역할도 하는게 'Getter & Setter'의 역할이다.**

#### **3\. Fields (public, private)**

 **생성자를 쓰지 않고 '필드'를 정의할 수 있는데, 그냥 정의하면 - public** 

  **#을 붙이면 - private : 'Class' 내부에서만 보이고 접근이 가능하다.**

**++**

**Too soon! 너무 최근에 추가되었다! 바벨을 이용해야 한다. 아직은 사용하기 이르다.**

**"이런 게 있구나"라는 정도로 넘어가자.**

```javascript
class Experiment{
    publicField = 2;
    #privateField = 0;
}
const experiment = new Experiment();
console.log(experiment.publicField);
console.log(experiment.privateField);
```

![](/assets/img/posts/49/4.png)

#### **4\. Static**

**'Class' 안에 있는 'Fields'와 'Methods'들은 'Object'를 만들때마다 그대로 복제된다.** 

**그래서 값만 우리가 지정한 값으로 변경된다.**

**간혹, 'Objsect'에 상관없이 'Class'가 가지고 있는 '고유한 값'과 동일하게 사용되어지는 'Method'가 있을 수 있다.**

**그런 것들을 'Static' 키워드를 사용하면 'Object'에 상관없이 'Class'자체에 연결되어 있다.**

```javascript
class Article{
    static publisher = 'Dream Coding';
    constructor(articleNumber){
        this.articleNumber = articleNumber;
    }
    static printPublisher(){
        console.log(Article.publisher);
    }
}
const article1 = new Article();
const article2 = new Article();

console.log(article1.publisher);
console.log(Article.publisher);
Article.printPublisher();
```

![](/assets/img/posts/49/5.png)

  
**★ 'Static'은 'Object'마다 붙어있느것이 아니라, 'Class'자체에 붙어있는 것이다.**

**++ 'Static'을 사용하는 것이 '메모리 효율'에 좋다**

#### **5\. Inheritance - 상속 + 다형성 + 재정의 (Overridng)**

**도형을 그린다고 했을 때, '너비', '높이'는 계속 반복된다. 계속해서 따로 만들기 보다는 '공통 코드'로 만들어 놓는게 좋다.**

```javascript
class Shape{
    constructor(width, height, color){
        this.width = width;
        this.height = height;
        this.color = color;
    }
    draw() {
        console.log(`drawing ${this.color} color of`);
    }
    getArea(){
        return this.width * this.width;
    }
}
```

```javascript
class Triangle1 extends Shape{}
class Rectangle extends Shape {}
const rectangle = new Rectangle(20, 20, 'red');
rectangle.draw();
console.log(rectangle.getArea()); //400 호출
const triangle1 = new Triangle1(30,30,'Yellow');
triangle1.draw();
console.log(triangle1.getArea()); // 900호출
```

![](/assets/img/posts/49/6.png)

**'Rectangle'이라는 '클래스'에서 'width', 'height', 'color'변수가 필요할 때,   
새로 만드는 것보다는 'extends' 키워드를 사용하여 'Shape'클래스를 상속 받아오는것이다.  
'Fields' & ''Methods'를 받아 올 수 있다.**

**이렇게 상속을 통해서, 공통적인 요소들은 재사용할 수 있다.  한곳만 수정하면 '유지보수'에도 좋다.**

**하지만!**

**'Triangle2'의 값은 '1/2'로 나누어야 한다는 새로운 기능이 필요할 때는?**

**여기서 '다형성'이 빛을 발휘한다. 필요한 함수만 '재정의'해서 사용한다 => 'Overriding'**

```javascript
class Triangle2 extends Shape{
    draw() {
        super.draw(); 
        console.log('New Triangle!!!!'); // Overriding
    }
    getArea(){
        return (this.width * this.height) / 2;  // getArea 메소드만 'Overriding'
    }
    toString(){
        return(`Triangle2 : ${this.color}`);
    }
}
const triangle2 = new Triangle2(30, 30, 'Yellow');
triangle2.draw();
console.log(`New : ${triangle2.getArea()}`);
```

![](/assets/img/posts/49/7.png)

**++ 기존 '부모'의 'draw'도 호출하고 싶으면 'super' 사용!**

#### **6\. Class checking : instanceOf**

**왼쪽의 'Object'가 오른쪽의 'Class'의 'Instace'인지 아닌지 확인하는 기능이다.**

**즉, 'Object'가 'Class'를 이용해서 만들어졌는지 확인하는 것.**

```javascript
console.log(rectangle instanceof Rectangle);
console.log(triangle1 instanceof Rectangle);
console.log(triangle1 instanceof Triangle1);
console.log(triangle1 instanceof Shape);
console.log(triangle1 instanceof Object);
```

![](/assets/img/posts/49/8.png)

**맨 마지막만 난해하고 나머지는 쉽게 이해했을 것이다.**

**마지막 코드 관련해서 설명하자면,** 

**'JavaSCript'에서 만든 모든 'Object', 'Class'는 'JavaScript'의 'Object'를 상속받은 것이다.**

**그래서 'True'가 Return된 것이다.**
