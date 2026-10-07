---
title: "날짜 데이터 - DATE vs VARCHAR(8)- 수치가 부적합합니다?"
date: 2022-06-28 17:05:52 +0900
categories: ["ETC", "IT Knowledge"]
tags: ["날짜 타입", "수치가 부적합합니다"]
tistory_url: "https://aroma-bok.tistory.com/entry/%EB%82%A0%EC%A7%9C-%EB%8D%B0%EC%9D%B4%ED%84%B0-DATE-vs-VARCHAR8-%EC%88%98%EC%B9%98%EA%B0%80-%EB%B6%80%EC%A0%81%ED%95%A9%ED%95%A9%EB%8B%88%EB%8B%A4"
---
**개발하면서 잘 돌아가던 서버가 갑자기 메인 페이지가 안 열리고 '수치가 부적합합니다.'라는 에러 메시지만 떴던 상황이 있었다. 이 상황은 아래와 같은 문제로 인해 발생한 일이었다.**

**날짜 데이터를 insert 할 때, '.'를 사용함으로써 데이터를 제대로 가공해서 사용할 수 없던 이유였다.**

  
**2022.06.21와 같이 구분자 '.'를 사용하지 말자.** 

**언제 어디서 사용될지 모르므로, 나중에 TO\_CHAR 또는 TO\_DATA로 변활 될 수 있도록 20220621과 같이 데이터를 insert 하자.**
