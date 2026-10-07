---
title: "[JavaScript] Array Interface 내용 + 사용법 + 적용 예"
date: 2021-12-30 22:37:10 +0900
categories: ["Language", "JavaScript"]
tags: ["array 인터페이스", "javascript interface", "자바스크립트", "자바스크립트 인터페이스"]
tistory_url: "https://aroma-bok.tistory.com/entry/JavaScript-Array-Interface-%EB%82%B4%EC%9A%A9-%EC%82%AC%EC%9A%A9%EB%B2%95-%EC%A0%81%EC%9A%A9-%EC%98%88"
---
#### **JavaScript에서 자주 사용하는 'Array Interface' 사용법과 적용 예시**

* * *

**interface Array<T\> {**

    **/\*\***

     **\* Gets or sets the length of the array. This is a number one higher than the highest index in the array.**

     **\*/**

    **length: number;**

**: 배열의 길이를 설정 또는 반환한다. 배열 인덱스보다 '1' 더 큰 길이이다.**

![](/assets/img/posts/55/1.png)

    **/\*\***

     **\* Returns a string representation of an array.**

     **\*/**

    **toString(): string;**

**: 배열을 'String'으로 표현한다.**

![](/assets/img/posts/55/2.png)

    **/\*\***

     **\* Returns a string representation of an array. The elements are converted to string using their toLocaleString methods.**

     **\*/**

    **toLocaleString(): string;**

**:  배열의 요소를 나타내는 문자열을 반환한다. 요소는 toLocaleString 메서드를 사용하여 문자열로 변환되고 이 문자열은 locale 고유 문자열(가령 쉼표 “,”)에 의해 분리됩니다.**

**: 생략하면 웹브라우저의 기본 Locale 값을 사용하지만 직접 지정할 수도 있다.**

![](/assets/img/posts/55/3.png)

    **/\*\***

     **\* Returns the value of the first element in the array where predicate is true, and undefined**

     **\* otherwise.**

     **\* @param predicate find calls predicate once for each element of the array, in ascending**

     **\* order, until it finds one where predicate returns true. If such an element is found, find**

     **\* immediately returns that element value. Otherwise, find returns undefined.**

     **\* @param thisArg If provided, it will be used as the this value for each invocation of**

     **\* predicate. If it is not provided, undefined is used instead.**

     **\*/**

    **find<S extends T\>(predicate: (this: void, value: T, index: number, obj: T\[\]) \=> value is S, thisArg?: any): S | undefined;**

    **find(predicate: (value: T, index: number, obj: T\[\]) \=> unknown, thisArg?: any): T | undefined;**

**: 주어진 판별 함수를 만족하는 **첫 번째 요소**의 **값**을 반환합니다. 그런 요소가 없다면 [undefined](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/undefined)를 반환합니다.**

![](/assets/img/posts/55/4.png)

  **/\*\***

     **\* Removes the last element from an array and returns it.**

     **\* If the array is empty, undefined is returned and the array is not modified.**

     **\*/**

    **pop(): T | undefined;**

**: pop() 메서드는 배열에서 마지막 요소를 제거하고 그 요소를 반환합니다.**

![](/assets/img/posts/55/5.png)

    **/\*\***

     **\* Appends new elements to the end of an array, and returns the new length of the array.**

     **\* @param items New elements to add to the array.**

     **\*/**

    **push(...items: T\[\]): number;**

**:push() 메서드는 배열의 끝에 하나 이상의 요소를 추가하고, 배열의 새로운 길이를 반환**

![](/assets/img/posts/55/6.png)

    **/\*\***

     **\* Combines two or more arrays.**

     **\* This method returns a new array without modifying any existing arrays.**

     **\* @param items Additional arrays and/or items to add to the end of the array.**

     **\*/**

    **concat(...items: ConcatArray<T\>\[\]): T\[\];**

**: 인자로 주어진 배열이나 값들을 기존 배열에 합쳐서 새 배열을 반환합니다.**

![](/assets/img/posts/55/7.png)

    **/\*\***

     **\* Adds all the elements of an array into a string, separated by the specified separator string.**

     **\* @param separator A string used to separate one element of the array from the next in the resulting string. If omitted, the array elements are separated with a comma.**

     **\*/**

    **join(separator?: string): string;**

**: 배열의 모든 요소를 연결해 하나의 문자열로 만듭니다.**

**: ()에 삽입한 문자로 하나의 문자열로 결합한다.**

![](/assets/img/posts/55/8.png)

    **/\*\***

     **\* Reverses the elements in an array in place.**

     **\* This method mutates the array and returns a reference to the same array.**

     **\*/**

    **reverse(): T\[\];**

**: 배열의 순서를 반전합니다. 첫 번째 요소는 마지막 요소가 되며 마지막 요소는 첫 번째 요소가 됩니다.**

**: 원래(original)의 배열 데이터 순서를 바꿔 놓기 때문에 주의 해야 한다!**

![](/assets/img/posts/55/9.png)

    **/\*\***

     **\* Removes the first element from an array and returns it.**

     **\* If the array is empty, undefined is returned and the array is not modified.**

     **\*/**

    **shift(): T | undefined;**

**:배열에서 첫 번째 요소를 제거하고, 제거된 요소를 반환합니다. 이 메서드는 배열의 길이를 변하게 합니다.**

![](/assets/img/posts/55/10.png)

    **/\*\***

     **\* Returns a copy of a section of an array.**

     **\* For both start and end, a negative index can be used to indicate an offset from the end of the array.**

     **\* For example, -2 refers to the second to last element of the array.**

     **\* @param start The beginning index of the specified portion of the array.**

     **\* If start is undefined, then the slice begins at index 0.**

     **\* @param end The end index of the specified portion of the array. This is exclusive of the element at the index 'end'.**

     **\* If end is undefined, then the slice extends to the end of the array.**

     **\*/**

    **slice(start?: number, end?: number): T\[\];**

**:어떤 배열의 begin부터 end까지(end 미포함)에 대한 얕은 복사본을 새로운 배열 객체로 반환합니다.**

**원본 배열은 바뀌지 않습니다.**

![](/assets/img/posts/55/11.png)

![](/assets/img/posts/55/12.png)

    **/\*\***

     **\* Sorts an array in place.**

     **\* This method mutates the array and returns a reference to the same array.**

     **\* @param compareFn Function used to determine the order of the elements. It is expected to return**

     **\* a negative value if the first argument is less than the second argument, zero if they're equal, and a positive**

     **\* value otherwise. If omitted, the elements are sorted in ascending, ASCII character order.**

     **\* \`\`\`ts**

     **\* \[11,2,22,1\].sort((a, b) => a - b)**

     **\* \`\`\`**

     **\*/**

    **sort(compareFn?: (a: T, b: T) \=> number): this;**

**:배열의 요소를 적절한 위치에 정렬한 후 그 배열을 반환합니다.**

**기본 정렬 순서는 문자열의 유니코드 코드 포인트를 따릅니다.**

![](/assets/img/posts/55/13.png)

    **/\*\***

     **\* Removes elements from an array and, if necessary, inserts new elements in their place, returning the deleted elements.**

     **\* @param start The zero-based location in the array from which to start removing elements.**

     **\* @param deleteCount The number of elements to remove.**

     **\* @returns An array containing the elements that were deleted.**

     **\*/**

    **splice(start: number, deleteCount?: number): T\[\];**

**: 배열의 기존 요소를 삭제 또는 교체하거나 새 요소를 추가하여 배열의 내용을 변경합니다.**

**: Return은 삭제된 요소가 출력됩니다.**

![](/assets/img/posts/55/14.png)

    **/\*\***

     **\* Removes elements from an array and, if necessary, inserts new elements in their place, returning the deleted elements.**

     **\* @param start The zero-based location in the array from which to start removing elements.**

     **\* @param deleteCount The number of elements to remove.**

     **\* @param items Elements to insert into the array in place of the deleted elements.**

     **\* @returns An array containing the elements that were deleted.**

     **\*/**

    **splice(start: number, deleteCount: number, ...items: T\[\]): T\[\];**

**: 위와 같습니다. 새 요소(items)를 추가하여 설명하는 글입니다.**

![](/assets/img/posts/55/15.png)

    **/\*\***

     **\* Inserts new elements at the start of an array, and returns the new length of the array.**

     **\* @param items Elements to insert at the start of the array.**

     **\*/**

    **unshift(...items: T\[\]): number;**

**: 새로운 요소를 배열의 맨 앞쪽에 추가하고, 새로운 길이를 반환합니다.**

![](/assets/img/posts/55/16.png)

    **/\*\***

     **\* Returns the index of the first occurrence of a value in an array, or -1 if it is not present.**

     **\* @param searchElement The value to locate in the array.**

     **\* @param fromIndex The array index at which to begin the search. If fromIndex is omitted, the search starts at index 0.**

     **\*/**

    **indexOf(searchElement: T, fromIndex?: number): number;**

******: 배열에서 지정된 요소를 찾을 수 있는 첫 번째 인덱스를 반환하고 존재하지 않으면 '-1'을 반환합니다.******

**: 'fromIndex'로 시작 인덱스를 지정할 수 있습니다.**

![](/assets/img/posts/55/17.png)

    **/\*\***

     **\* Returns the index of the last occurrence of a specified value in an array, or -1 if it is not present.**

     **\* @param searchElement The value to locate in the array.**

     **\* @param fromIndex The array index at which to begin searching backward. If fromIndex is omitted, the search starts at the last index in the array.**

     **\*/**

    **lastIndexOf(searchElement: T, fromIndex?: number): number;**

**: 배열에서 주어진 값을 발견할 수 있는 마지막 인덱스를 반환하고, 요소가 존재하지 않으면 -1을 반환합니다.**

****: 배열 탐색은 fromIndex에서 시작하여 뒤로 진행합니다.****

![](/assets/img/posts/55/18.png)

    **/\*\***

     **\* Determines whether all the members of an array satisfy the specified test.**

     **\* @param predicate A function that accepts up to three arguments. The every method calls**

     **\* the predicate function for each element in the array until the predicate returns a value**

     **\* which is coercible to the Boolean value false, or until the end of the array.**

     **\* @param thisArg An object to which the this keyword can refer in the predicate function.**

     **\* If thisArg is omitted, undefined is used as the this value.**

     **\*/**

    **every<S extends T\>(predicate: (value: T, index: number, array: T\[\]) \=> value is S, thisArg?: any): this is S\[\];**

**: 배열 안의 모든 요소가 주어진 판별 함수를 통과하는지 테스트합니다. Boolean 값을 반환합니다.** 

**참고: 빈 배열에서 호출하면 무조건 true를 반환합니다!**

![](/assets/img/posts/55/19.png)

    **/\*\***

     **\* Determines whether the specified callback function returns true for any element of an array.**

     **\* @param predicate A function that accepts up to three arguments. The some method calls**

     **\* the predicate function for each element in the array until the predicate returns a value**

     **\* which is coercible to the Boolean value true, or until the end of the array.**

     **\* @param thisArg An object to which the this keyword can refer in the predicate function.**

     **\* If thisArg is omitted, undefined is used as the this value.**

     **\*/**

    **some(predicate: (value: T, index: number, array: T\[\]) \=> unknown, thisArg?: any): boolean;**

**: 배열 안의 어떤 요소라도 주어진 판별 함수를 통과하는지 테스트합니다.**

![](/assets/img/posts/55/20.png)

    **/\*\***

     **\* Performs the specified action for each element in an array.**

     **\* @param callbackfn  A function that accepts up to three arguments. forEach calls the callbackfn function one time for each element in the array.**

     **\* @param thisArg  An object to which the this keyword can refer in the callbackfn function. If thisArg is omitted, undefined is used as the this value.**

     **\*/**

    **forEach(callbackfn: (value: T, index: number, array: T\[\]) \=> void, thisArg?: any): void;**

**: 주어진 함수를 배열 요소 각각에 대해 실행합니다.**

![](/assets/img/posts/55/21.png)

    **/\*\***

     **\* Calls a defined callback function on each element of an array, and returns an array that contains the results.**

     **\* @param callbackfn A function that accepts up to three arguments. The map method calls the callbackfn function one time for each element in the array.**

     **\* @param thisArg An object to which the this keyword can refer in the callbackfn function. If thisArg is omitted, undefined is used as the this value.**

     **\*/**

    **map<U\>(callbackfn: (value: T, index: number, array: T\[\]) \=> U, thisArg?: any): U\[\];**

**:배열 내의 모든 요소 각각에 대하여 주어진 함수를 호출한 결과를 모아 새로운 배열을 반환합니다.**

![](/assets/img/posts/55/22.png)

    **/\*\***

     **\* Returns the elements of an array that meet the condition specified in a callback function.**

     **\* @param predicate A function that accepts up to three arguments. The filter method calls the predicate function one time for each element in the array.**

     **\* @param thisArg An object to which the this keyword can refer in the predicate function. If thisArg is omitted, undefined is used as the this value.**

     **\*/**

    **filter<S extends T\>(predicate: (value: T, index: number, array: T\[\]) \=> value is S, thisArg?: any): S\[\];**

    **filter(predicate: (value: T, index: number, array: T\[\]) \=> unknown, thisArg?: any): T\[\];**

**: 주어진 함수의 테스트를 통과하는 모든 요소를 모아 새로운 배열로 반환합니다.**

![](/assets/img/posts/55/23.png)

    **/\*\***

     **\* Calls the specified callback function for all the elements in an array. The return value of the callback function is the accumulated result, and is provided as an argument in the next call to the callback function.**

     **\* @param callbackfn A function that accepts up to four arguments. The reduce method calls the callbackfn function one time for each element in the array.**

     **\* @param initialValue If initialValue is specified, it is used as the initial value to start the accumulation. The first call to the callbackfn function provides this value as an argument instead of an array value.**

     **\*/**

    **reduce(callbackfn: (previousValue: T, currentValue: T, currentIndex: number, array: T\[\]) \=> T): T;**

    **reduce(callbackfn: (previousValue: T, currentValue: T, currentIndex: number, array: T\[\]) \=> T, initialValue: T): T;**

**: 배열의 각 요소에 대해 주어진 리듀서(reducer) 함수를 실행하고, 하나의 결과값을 반환합니다.**

**: 'return'값이 다음 호출의 'Prev'값이 된다. ※값을 누적할 때 사용!**

![](/assets/img/posts/55/24.png)

    **/\*\***

     **\* Calls the specified callback function for all the elements in an array, in descending order. The return value of the callback function is the accumulated result, and is provided as an argument in the next call to the callback function.**

     **\* @param callbackfn A function that accepts up to four arguments. The reduceRight method calls the callbackfn function one time for each element in the array.**

     **\* @param initialValue If initialValue is specified, it is used as the initial value to start the accumulation. The first call to the callbackfn function provides this value as an argument instead of an array value.**

     **\*/**

    **reduceRight(callbackfn: (previousValue: T, currentValue: T, currentIndex: number, array: T\[\]) \=> T): T;**

    **reduceRight(callbackfn: (previousValue: T, currentValue: T, currentIndex: number, array: T\[\]) \=> T, initialValue: T): T;**

**:누적기에 대해 함수를 적용하고 배열의 각 값 (오른쪽에서 왼쪽으로)은 값을 단일 값으로 줄여야합니다.**

**initialValue가 제공되지 않으면 previousValue는 배열의 마지막 값과 같고 currentValue는 두 번째 - 마지막 값과 같습니다.**

![](/assets/img/posts/55/25.png)

![](/assets/img/posts/55/26.png)

  

    **\[n: number\]: T; }**
