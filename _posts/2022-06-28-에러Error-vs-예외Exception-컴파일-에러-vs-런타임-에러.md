---
title: "에러(Error) vs 예외(Exception) (컴파일 에러 vs 런타임 에러)"
date: 2022-06-28 17:01:32 +0900
categories: ["ETC", "IT Knowledge"]
tags: ["런타임 오류", "에러", "에러 vs 예외", "에러 예외 차이", "오류", "컴파일 에러"]
tistory_url: "https://aroma-bok.tistory.com/entry/%EC%97%90%EB%9F%ACError-vs-%EC%98%88%EC%99%B8Exception-%EC%BB%B4%ED%8C%8C%EC%9D%BC-%EC%97%90%EB%9F%AC-vs-%EB%9F%B0%ED%83%80%EC%9E%84-%EC%97%90%EB%9F%AC"
---
**개발을 하면서 아무 생각 없이 에러, 예외를 try~catch구문을 사용해서 catch로 던져주면 되지 않나라고 생각했던 적이 있다.** **잘못된 생각을 고치기 위해서 개념을 정리해보았다.**

* * *

  
  
**에러** **: 프로그램을 돌렸을 때, 오작동이나 비정상적으로 종료되는 원인을 오류, 에러라고 한다. 발생 시, 수습할 수 없는 심각한 오류 (시스템 레벨에서 발생)**

**\- 컴파일 에러 : 컴파일 시 에러**  
**\- 런타임 에러 : 실행 도중에 발생하는 에러**

**예외 : 예외 처리를 통해 수습할 수 있는 덜 심각한 오류 (구현한 로직에서 발생)**

  
**에러(Error)는 시스템 레벨의 심각한 수준의 에러이기 때문에 시스템에 변화를 주어 문제를 처리해야 하는 경우가 일반적.**  
**반면에, 예외(Exception)는 개발자가 로직을 추가하여 처리할 수 있다.**  
  

![](/assets/img/posts/148/1.png)

  
**그림에서 예외(Exception) 클래스를 상속받는 Runtime Exception이 'Checked Exception'과 'Unchecked Exception'을 나누는 기준이다.**  
  

![](/assets/img/posts/148/2.png)

  
  
**'Checked Exception'과 'Unchecked Exception'의 가장 명확한 구분 기준은 '꼭 처리를 해야 하느냐'이다.**  
**'Checked Exception'이 발생할 가능성이 있는 메서드라면 반드시 로직을 try~catch로 감싸거나 throw로 던져서 처리해야 한다.**  
  
**반면에 'UncheckedException'은 명시적인 예외처리를 하지 않아도 된다. 이 예외는 피할 수 있지만 개발자가 부주의해서 발생하는 경우가 대부분이고, 미리 예측하지 못했던 상황에서** **발생하는 예외가 아니기 때문에 굳이 로직으로 처리를 할 필요가 없도록 만들어져 있다.**  
  
**예외를 확인할 수 있는 시점에서도 구분할 수 있다. 일반적으로 컴파일 단계에서 명확하게 Exception 체크가 가능한 것을 'Checked Exception'이라 하며,** **실행 과정 중 어떠한 특정 논리에 의해 발견되는 Exception을 'Unchecked Exception'이라 한다.**  
  
**그리고 예외 발생 시, 트랜잭션의 'roll-back'여부는 알아두면 좋다고 한다. 기본적으로 'Checked Exception'은 예외가 발생하면 트랜잭션을 'roll-back'하지 않고 예외를 던져준다.**  
**하지만 'Unchecked Exception'은 예외가 발생하면 트랜잭션을 'roll-back'한다는 점에서 차이가 있다.**
