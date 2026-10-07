---
title: "[JavaScript] Web 개발을 위한 JavaScript (3) - 데이터 타입 +  let vs var + Constant + Backtick + Dynamic Typing"
date: 2021-12-21 13:46:41 +0900
categories: ["Language", "JavaScript"]
tags: ["backtick", "boolean", "global scope", "javscript", "js", "let", "number", "string", "var", "변수"]
tistory_url: "https://aroma-bok.tistory.com/entry/JavaScript-Web-%EA%B0%9C%EB%B0%9C%EC%9D%84-%EC%9C%84%ED%95%9C-JavaScript-2-%EB%8D%B0%EC%9D%B4%ED%84%B0-%ED%83%80%EC%9E%85-let-vs-var-Constant-Backtick-Dynamic-Typing"
---
### **데이터 타입 +  let vs var + Constant + Backtick + Dynamic Typing**

* * *

#### **1\. 변수의 선언과 할당**

**변수 : 변경될 수 있는 값 + rw(read/write)가 가능하다**

**let : ECMAScript(ES) 6에 추가된 변수** 

**(\* 요즘엔 ES6미만으로는 잘 사용하지 않는다고 한다.)**

![](/assets/img/posts/46/1.png)

![](/assets/img/posts/46/2.png)

*Console 결과*

#### **2\. 변수의 범위(Scope)**

**블록 안에 있는 변수는 블록 하위에서는 사용할 수가 없다.**

![](/assets/img/posts/46/3.png)

![](/assets/img/posts/46/4.png)

*Console 결과*

#### **3\. Global Scope (글로벌 변수) - 전역변수**

**반대로, 블록에 정의하지 않고 파일 안에다가 생성해서 쓰는 변수를 'Global Scope'라고 부른다고 한다.**

**'Global Scope'는 블록 안에서나 밖에서나 어디든지 이용 가능하다. JAVA에서의 'Static'과 비슷한 개념이라고 볼 수 있다.**

**'글로벌 변수'는 애플리케이션이 시작하고 끝날 때까지 '메모리'에 할당되어 있기 때문에 '최소한'으로 사용하는 것이 좋다.**

**클래스나 함수와 같이 필요한 부분에서만 정의해서 사용하는 것이 좋다.**

![](/assets/img/posts/46/5.png)

![](/assets/img/posts/46/6.png)

*Console 결과*

#### **4\. let vs var**

**주변에 아직까지도 var를 쓰고 있다면 등짝을 때리면서 "쓰지 마!"라고 할 정도로 안 쓰는 변수 타입이라고 한다...ㅎㅎ** 

**보통 변수를 '선언'하고 '할당'을 하는 게 일반적이다. 하지만 'var'는 할당을 먼저 하고 선언을 해도 에러로 인식하지 않는다.**

**그리고 선언을 가장 나중에 해도 console에서는 var가 정의되어 있다고 인식하는 이상한? 기능도 한다.**

**이러한걸 'Hoisting'이라고 한다.**

**Hoisting : 어디에 '선언'을 했냐에 상관없이 항상 '선언'을 제일 위로 끌어올려주는 것을 말한다.**

![](/assets/img/posts/46/7.png)

![](/assets/img/posts/46/8.png)

*정의는 되어있지만 값이 할당되지 않음으로 인식*

![](/assets/img/posts/46/9.png)

![](/assets/img/posts/46/10.png)

*신기하다...*

**반면에, 'let'을 사용하면 'var'에서 인식하지 않은 에러를 인식해서 보여준다.**

**할당도 되기 전에 Console에서 사용하는 게 당연히 말이 안 되는 거다.**

![](/assets/img/posts/46/11.png)

![](/assets/img/posts/46/12.png)

**그리고 한 가지 더! 'var'는 '블록 범위'가 없다. '변수'는 '유효 범위'를 가지고 있다. 하지만 'var'는 '유효범위'를 가지고 있지 않다.**

**초창기에는 이러한 기능을 이용해서 프로젝트를 빠르게 대충 만들었지만,**

**어느 정도 시간이 지나고 규모가 있는 프로젝트에서는 선언하지 않은 값들이 할당되는 이상한 경우가 발생된다.**

**그래서 이러한 이유 때문에 'var'를 사용하지 않는다.**

![](/assets/img/posts/46/13.png)

![](/assets/img/posts/46/14.png)

**var를 사용하면 안 되는 이유**

**1\. Hoisting** 

**2\. has no block scope (블록 범위가 없다.)**

#### **5\. 컨스턴트(Constant)** 

**Constant - r(read only)**

**\- use const whenever possible.**

**\- only use let if variable needs to change.**

**변수는 계속 값을 바꿔갈 수 있었던 반면에, 값을 한번 할당하면 '절대' 값이 바뀌지 않는 값을 '컨스턴트(Constant)'라 부른다.** 

**Mutable - 값이 계속 바뀔 수 있는 타입 : let**

**++ Mutable data types : all objects by default are mutable in JS**

**Immutable - 값이 바뀔 수 없는 타입 : Constant  
++Immutable data types : primitive types, frozen objects(i.e. object.freeze())**

![](/assets/img/posts/46/15.png)

![](/assets/img/posts/46/16.png)

*한번 할당되면 값을 변경할 수 없다.*

**컨스턴트(Constant)를 사용하는 이유** 

**1) Security**

**2) Thread Safety**  
**: 애플리케이션이 실행되면 'Thread'라는 게 실행되는데 여러 개의 'Thread'가 변수에 값을 할당할 수 있기 때문에**

**그것을 방지하고자 함.**

**3) Reduce human mistakes**

#### **6.Variable Types (변수 타입)**

**1) Primitive \- Single item : number, string, boolean, null, undefiedn, symbol**

**2) Object - box container ('Single item'을 묶어서 한 단위, 한 박스로 관리할 수 있도록 하는 것.)**

**3) function - first-class function (함수를 변수, 인자, return타입으로도 가능하다.)**

#### **6-1) Number**

**C언어 or Java에서는 미리 어느 정도 할당한 것인지에 대해서 number에 관한 'data type'을 생각해야 한다.**

**하지만, JavaScript는 'number' 하나만으로 사용 가능하다!**

**그리고 굳이 number를 선언해서 값을 '할당'하지 않아도 '할당'만 하면 자동으로 타입을 결정해준다!**

![](/assets/img/posts/46/17.png)

![](/assets/img/posts/46/18.png)

*정수, 실수 상관없이 number로 인식!*

**\* number에서도 좀 특별한 값이 있다.** 

**1) 유효한 숫자를 '0'으로 나누는 경우 - Infinity**

**2) 마이너스이면서 유효한 숫자를 '0'으로 나누는 경우 - -Infinity**

**3) 숫자가 아닌 값을 '0'으로 나누는 경우 - NaN**

**\* 항상 연산할 때, 그 값이 유효한 숫자인지 확인하고 연산을 진행해야 한다.**

![](/assets/img/posts/46/19.png)

![](/assets/img/posts/46/20.png)

**JavaScript에서의 'number'는 '-2^53~ 2^53' 범위의 숫자만 표현이 가능하다**

**하지만, 최근에 'bigInt' 타입이 추가되었다. 값 마지막에 'n'만 붙여주면 'bigInt'로 인식한다.**

**(최근에 추가되었기 때문에, 아직은 '파이어폭스'와 '크롬'에서만 지원을 하고 있다고 한다. 흔하게 사용되지는 않는다.)**

![](/assets/img/posts/46/21.png)

![](/assets/img/posts/46/22.png)

#### **6-2) String**

**String 같은 경우에는, 1개의 글자든 여러 개의 글자든 다 'String' 타입으로 할당이 된다.**

**그리고 많이 쓰이는 'Template literals || Template string'이라 불리는 방법이 있다.**

**Backtick(\`)을 사용하면서 안에 ${}를 이용하여 변수를 사용하는 방법이다.**

**(기존에는 '+'를 사용해서 조금 번거로운? 방법을 사용했는데, 이번에 이 방법을 배워서 유용하게 써먹을 수 있을 거 같다고 생각한다.)**

![](/assets/img/posts/46/23.png)

![](/assets/img/posts/46/24.png)

*'Backtick' 사용 여부 차이*

#### **6-3) boolean**

**false : 0, null, undefined, Nan, ' '** 

**true : any other value**

![](/assets/img/posts/46/25.png)

![](/assets/img/posts/46/26.png)

**6-4) null vs undefeind**

**'null'은 안에 텅 비어있는 empty 값이야. 즉, null로 값이 할당이 되어있는 상태.**

**'undefeind'는 선언은 되어있지만, 값이 어떤 것도 들어가 있지 않은 상태.**

![](/assets/img/posts/46/27.png)

![](/assets/img/posts/46/28.png)

#### **6-5) Symbol**

**Map이나 다른 자료구조에서 특별한 식별자가 필요하거나, 아니면 동시 다발적으로 일어날 수 있는 코드에서 '우선순위'를 주고 싶을 때, 즉 '고유한 식별자'가 필요할 때 사용하는 타입. 주어지는 String에 상관없이 '고유한 식별자'가 만들어진다.**

![](/assets/img/posts/46/29.png)

![](/assets/img/posts/46/30.png)

**반대로 같은 'String'을 사용해서 '식별자'를 동일하게 표현하고 싶으면, 'Symbol.for'를 사용하면 된다.**

![](/assets/img/posts/46/31.png)

![](/assets/img/posts/46/32.png)

**그리고 'Symbol'은 바로 출력이 안되기 때문에 'description'을 통해서 'String'으로 변환해서 출력해야 한다.**

![](/assets/img/posts/46/33.png)

![](/assets/img/posts/46/34.png)

*description을 사용하지 않으면 출력 불가*

#### **6-6) Object**

**일상생활에서 보는 물건과 물체들을 대표하는 '박스 형태'**

**한번 할당된 Object는 Constant로 선언되었기 때문에 다른 Object로 변경이 불가능하다.**

**하지만, 안에 할당된 값들은 변경이 가능하다.**

![](/assets/img/posts/46/35.png)

![](/assets/img/posts/46/36.png)

#### **7\. Dynamic typing : dynamically typed language** 

**'JavaScript'는 'dynamically typed language - 동적 프로그래밍 언어 '라고 불린다.**

**'C언어', 'Java'는 'statically typed language -  정적 프로그래밍 언어'라고 불린다.**

**'JavaScript'는 C언어, Java와는 달리, 변수를 선언할 때, 변수의 타입을 설정하지 않는다.**

**값을 대입하면 자동으로 변수의 타입이 변경되기 때문이다.**

**하지만, 이러한 특징이 꼭 좋은 점만은 아니다. 밑에 예제를 보면서 설명을 해보겠다.**

 **String값이 들어간 경우에는 당연 String으로 인식하고 출력한다. Number도 마찬가지이다.**

![](/assets/img/posts/46/37.png)

![](/assets/img/posts/46/38.png)

![](/assets/img/posts/46/39.png)

![](/assets/img/posts/46/40.png)

**그리고 String값에 number를 더하거나, String인데 나누기(/)를 사용하면 아래와 같은 결과를 볼 수 있다.**

![](/assets/img/posts/46/41.png)

![](/assets/img/posts/46/42.png)

**또 위험한 점은, 'String' 타입일 때는 'charAt'을 사용해서 인덱싱을 얻어올 수 있지만, 다른 개발자와 함께 개발하다가 'Number'타입으로 바뀌면서 'charAt'을 사용하지 못하는 경우가 발생한다.**

![](/assets/img/posts/46/43.png)

![](/assets/img/posts/46/44.png)

![](/assets/img/posts/46/45.png)

*Number타입 상태에서 사용한 경우*

**'JavaScript'는 '런타임'에서 'DataType'이 결정되기 때문에 '런타임 오류'가 발생하는 경우가 많다고 한다.**

**\* 그래서 'JavaScript'위에 'Type'이 올려진 언어인 'TypeScript'가 나왔다고 한다.** 

**'JavaScript'를 공부하고 'TypeScript'로 넘어가는 게 좋다고 한다. (TS는 실시간으로 브라우저를 통해서 볼 수 없다고 한다.)**

**출처 : https://www.youtube.com/watch?v=OCCpGh4ujb8**
