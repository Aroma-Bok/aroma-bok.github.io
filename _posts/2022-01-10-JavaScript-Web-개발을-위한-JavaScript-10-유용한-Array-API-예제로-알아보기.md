---
title: "[JavaScript] 유용한 Array API 예제로 알아보기!"
date: 2022-01-10 23:37:44 +0900
categories: ["Language", "JavaScript"]
tags: ["array", "array 예제", "array.api"]
tistory_url: "https://aroma-bok.tistory.com/entry/JavaScript-Web-%EA%B0%9C%EB%B0%9C%EC%9D%84-%EC%9C%84%ED%95%9C-JavaScript-10-%EC%9C%A0%EC%9A%A9%ED%95%9C-Array-API-%EC%98%88%EC%A0%9C%EB%A1%9C-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0"
---
**문제 / 답 순서로 정리하였습니다. 참고하세요.**

```javascript
// Q1. make a string out of an array
{
  const fruits = ['apple', 'banana', 'orange'];
}
```

```javascript
// Q1. make a string out of an array
{
  const fruits = ['apple', 'banana', 'orange'];
  console.log(fruits.toString());     // Me
  console.log(fruits.join('-'));      // Ellie
}
```

```javascript
// Q2. make an array out of a string
{
  const fruits = '🍎, 🥝, 🍌, 🍒';
  }
```

```javascript
// Q2. make an array out of a string
{
  const fruits = '🍎, 🥝, 🍌, 🍒';
  let a = [];
  a = [fruits];
  console.log(a); // Me

  // 문자열안에 있는 'Api' 'split' 사용하기
  console.log(fruits.split(',')); // Ellie
}
```

```javascript
// Q3. make this array look like this: [5, 4, 3, 2, 1]
{
  const array = [1, 2, 3, 4, 5];
}
```

```javascript
// Q3. make this array look like this: [5, 4, 3, 2, 1]
{
  const array = [1, 2, 3, 4, 5];
  console.log(array.reverse());

  // 기존의 'array'의 순서도 바뀌니까 주의!
}
```

```javascript
// Q4. make new array without the first two elements
{
  const array = [1, 2, 3, 4, 5];
}
```

```javascript
// Q4. make new array without the first two elements
{
  const array = [1, 2, 3, 4, 5];
  // array.shift();
  // array.shift();
  // console.log(array);
  //console.log(array.splice(0,2));
  //console.log(array);

  // 위 방법들은 기존의 배열이 변경되기 때문에 새로운 배열이 생성되는게 아니다. 그러니까 정답이 아니다.

  // splice vs slice 차이점을 알아두자!

  // 원본 배열이 바뀌지 않는 'slice' 사용하기
  console.log(array.slice(2));
  console.log(array);

}
```

#### **5번~10번 묶음**

```javascript
class Student {
  constructor(name, age, enrolled, score) {
    this.name = name;
    this.age = age;
    this.enrolled = enrolled;
    this.score = score;
  }
}
const students = [
  new Student('A', 29, true, 45),
  new Student('B', 28, false, 80),
  new Student('C', 30, true, 90),
  new Student('D', 40, false, 66),
  new Student('E', 18, true, 88),
];

// Q5. find a student with the score 90
{

}
```

```javascript
class Student {
  constructor(name, age, enrolled, score) {
    this.name = name;
    this.age = age;
    this.enrolled = enrolled;
    this.score = score;
  }
}
const students = [
  new Student('A', 29, true, 45),
  new Student('B', 28, false, 80),
  new Student('C', 30, true, 90),
  new Student('D', 40, false, 66),
  new Student('E', 18, true, 88),
];

// Q5. find a student with the score 90
{
  // students.map(student, index => {if(student.score === 90)
  //   console.log(student);
  // } );

  // find 함수 : 첫번째로 'true'인 값을 리턴해준다.
  const result = students.find((student) => student.score === 90)
  console.log(result);
}
```

```javascript
// Q6. make an array of enrolled students
{

}
```

```javascript
// Q6. make an array of enrolled students
{
  // let enrolledStudent = students.filter(student => student.enrolled === true);
  let enrolledStudent = students.filter(student => student.enrolled);
  console.log(enrolledStudent);
}
```

```javascript
// Q7. make an array containing only the students' scores
// result should be: [45, 80, 90, 66, 88]
{
 
}
```

```javascript
// Q7. make an array containing only the students' scores
// result should be: [45, 80, 90, 66, 88]
{
  // let scroeStudents = students.map(student => );
  const studentScore = students.map(student => student.score)
  console.log(studentScore);

  // '콜백함수'로 주어지는 '인자'는 누구나 봤을 때, 이해하기 쉽게 이름을 짓는 습관을 들이자! 
}
```

```javascript
// Q8. check if there is a student with the score lower than 50
{

}
```

```javascript
// Q8. check if there is a student with the score lower than 50
{
  console.log(students.some(student => student.score < 50 ));
  console.log(students.every(student => student.score >= 50 ));
}
```

```javascript
// Q9. compute students' average score
{

}
```

```javascript
// Q9. compute students' average score
{
  // let sumScore = 0;
  // students.forEach(student => sumScore += student.score );
  // let lengthScore = students.length;
  // console.log(sumScore);
  // console.log(lengthScore);
  // console.log(sumScore / lengthScore);

  // 'reduce'함수 사용하기! 
  // 'reduce'함수는 'return'값이 다음 호출의 'Prev'값이 된다.
  // 값을 누적할 때 사용한다!
  let scoreSum = students.reduce((prev, curr) => {
    console.log(prev);
    console.log(curr);
    return prev + curr.score;
  },0);
  console.log(scoreSum / students.length);
  // console.log(scoreSum);
}
```

```javascript
// Q10. make a string containing all the scores
// result should be: '45, 80, 90, 66, 88'
{

}
```

```javascript
// Q10. make a string containing all the scores
// result should be: '45, 80, 90, 66, 88'
{
  const studentScore = students
    .map(student => student.score)
    .filter((score) => score >=50)
  console.log(studentScore.toString());
  console.log(studentScore.join());
}
```

```javascript
// Bonus! do Q10 sorted in ascending order
// result should be: '45, 66, 80, 88, 90'
{

}
```

```javascript
// Bonus! do Q10 sorted in ascending order
// result should be: '45, 66, 80, 88, 90'
{
  const studentScore = students.map(student => student.score)
  console.log(studentScore.sort().toString()); // 오름차순

  console.log(studentScore.sort((A,B) => B-A).toString()); // 내림차순
}
```
