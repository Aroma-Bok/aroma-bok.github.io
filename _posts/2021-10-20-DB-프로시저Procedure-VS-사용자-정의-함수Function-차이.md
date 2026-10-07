---
title: "[DB] 프로시저(Procedure) VS 사용자 정의 함수(Function) 차이"
date: 2021-10-20 23:54:24 +0900
categories: ["Solution & Tools", "DB"]
tags: ["db", "function", "mysql", "procedure", "sql", "프로시저 함수 차이"]
tistory_url: "https://aroma-bok.tistory.com/entry/DB-%ED%94%84%EB%A1%9C%EC%8B%9C%EC%A0%80Procedure-VS-%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%A0%95%EC%9D%98-%ED%95%A8%EC%88%98Function-%EC%B0%A8%EC%9D%B4"
---
프로젝트를 진행하다가 프로시저(Procedure)를 사용할 때 있고, 함수(Function)를 사용할 때가 있는 걸 알게 되었다.

제대로 DB를 공부해본 적이 없는 나로서는 둘의 차이가 뭔지 궁금증을 갖게 되었다.

* * *

<table style="border-collapse: collapse; width: 100.813%; height: 138px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 110px;"><td style="width: 100%; height: 110px;"><h4 data-ke-size="size20"><span style="font-family: 'Noto Serif KR';">프로시저(Procedure) 란?</span></h4><span style="font-family: 'Noto Serif KR';">SQL에서 제공하는 프로그래밍 기능으로서 쿼리문을 마치 하나의 메서드 형식으로 만들고 어떠한 동작을 일괄적으로 처리하는 용도<br></span></td></tr></tbody></table>

```sql
CREATE OR REPLACE PROCEDURE 프로시저 이름

( 매개변수명1[ IN | OUT | IN OUT ] 데이터타입[:= 디폴트값],
  매개변수명2[ IN | OUT | IN OUT ] 데이터타입[:= 디폴트값], ... )
IS[AS]
변수, 상수 등 선언
BEGIN
실행

[EXCEPTION 예외처리부]

END [프로시저 이름]
```

<table style="letter-spacing: 0px; border-collapse: collapse; width: 100.233%; height: 110px;" border="1" data-ke-align="alignLeft"><tbody><tr><td style="width: 100%;"><h4 data-ke-size="size20"><span style="font-family: 'Noto Serif KR';">사용자 정의 함수(Function) 란?</span></h4><span style="font-family: 'Noto Serif KR';">절차형 SQL을 활용하여 일련의 SQL 처리를 수행하고, 수행 결과를 단일 값으로 반환할 수 있는 절차형 SQL</span></td></tr></tbody></table>

```sql
CREATE OR REPLACE FUNCTION 함수 이름 (매개변수1, 매개변수2....)

RETURN 데이터 타입;

IS[AS]

변수, 상수 선언..

BEGIN

실행부

RETURN 반환값

[EXCEPTION 예외처리부]

END [함수 이름];
```

내 기준에서는 구조도 같아 보이고,  결국 값을 얻기 위함이면 굳이 왜 나눠서 사용하지? 이런 생각이 든다.

그래서 좀 더 알아보았다.

<table style="border-collapse: collapse; width: 100%; height: 80px;" border="1" data-ke-align="alignLeft" data-ke-style="style12"><tbody><tr style="height: 20px;"><td style="width: 50%; height: 20px; text-align: center;"><span style="font-family: 'Noto Serif KR';"><b>프로시저</b></span></td><td style="width: 50%; height: 20px; text-align: center;"><span style="font-family: 'Noto Serif KR';"><b>함수</b></span></td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;"><span style="font-family: 'Noto Serif KR';">작업 수행</span></td><td style="width: 50%; height: 20px;"><span style="font-family: 'Noto Serif KR';">계산 수행</span></td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;"><span style="font-family: 'Noto Serif KR';">리턴값 0개 or 1개 or 여러개</span></td><td style="width: 50%; height: 20px;"><span style="font-family: 'Noto Serif KR';">리턴값 오직 1개</span></td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;"><span style="font-family: 'Noto Serif KR';">서버에서 실행 (속도가 더 빠름)</span></td><td style="width: 50%; height: 20px;"><span style="font-family: 'Noto Serif KR';">클라이언트에서 실행</span></td></tr><tr><td style="width: 50%;"><span style="font-family: 'Noto Serif KR';">SELECT 문에서 호출 불가능 (EXEC or EXECUTE로 호출)</span></td><td style="width: 50%;"><span style="background-color: #efefef; font-family: 'Noto Serif KR';">SELECT 문에서 호출 가능</span></td></tr></tbody></table>

개인적으로 간추려보면, **프로시저는 복잡한 트랜잭션을 수행, 연속 실행(배치)을 위해서 사용하고,**

**함수는 단순한 계산을 수행해서 값을 얻기 위해서 사용하는 것 같다.** 

솔직히 아직도 의문인 건, 굳이 그럼 나눠서 사용 안 하고 프로시저로 돌리면 안 되는 건가?라는 생각이 든다.

해결하지 못한 궁금증은 차근차근 알아가 봐야겠다.
