---
title: "[SQL] LISTAGG 함수 개념과 사용예시"
date: 2023-08-28 10:07:04 +0900
categories: ["Solution & Tools", "DB"]
tags: ["listagg"]
tistory_url: "https://aroma-bok.tistory.com/entry/SQL-LISTAGG-%ED%95%A8%EC%88%98-%EA%B0%9C%EB%85%90%EA%B3%BC-%EC%82%AC%EC%9A%A9%EC%98%88%EC%8B%9C"
---
#### **LISTAGG**

**Oracle 데이터베이스에서 사용되는 집계 함수 중 하나로, 특정 칼럼의 값을 그룹으로 묶어 하나의 문자열로 합치는 기능**

**그룹화된 데이터를 쉽게 문자열로 만들어서 조회 가능하며, 결과를 단일 문자열 값으로 반환한다.**

```sql
LISTAGG(column_name, delimiter) WITHIN GROUP (ORDER BY ordering_column) AS aggregated_column
```

-   **column\_name : 합치고자 하는 컬럼의 이름**
-   **delimiter : 합쳐진 값들을 구분할 구분자**
-   **WITHIN GROUP : ORDER BY 절과 함께 사용되며, ORDER BY 절에 지정한 표현식을 기준으로 값들을 정렬한 후에 LISTAGG 함수가 값을 결합**
-   **ORDER BY ordering\_column : 결과 문자열의 순서를 결정하기 위한 컬럼**
-   **aggregated\_column : 별칭**

* * *

**예시**

**employees 테이블**

<table style="border-collapse: collapse; width: 100%; height: 68px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 17px;"><td style="width: 25%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">emp_id</span></b></td><td style="width: 25%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">emp_name</span></b></td><td style="width: 25%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">dept_id</span></b></td></tr><tr style="height: 17px;"><td style="width: 25%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">101</span></b></td><td style="width: 25%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">Alice</span></b></td><td style="width: 25%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">1</span></b></td></tr><tr style="height: 17px;"><td style="width: 25%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">102</span></b></td><td style="width: 25%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">Bob</span></b></td><td style="width: 25%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">1</span></b></td></tr><tr style="height: 17px;"><td style="width: 25%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">103</span></b></td><td style="width: 25%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">Carol</span></b></td><td style="width: 25%; height: 17px;"><b><span style="font-family: 'Noto Serif KR';">2</span></b></td></tr><tr><td style="width: 25%;"><b><span style="font-family: 'Noto Serif KR';">104</span></b></td><td style="width: 25%;"><b><span style="font-family: 'Noto Serif KR';">David</span></b></td><td style="width: 25%;"><b><span style="font-family: 'Noto Serif KR';">2</span></b></td></tr></tbody></table>

**'dept\_id' 그룹별로 'emp\_name'을 ', ' 구분자로 합쳐서 출력한다고 가정하자.**

**그리고 정렬은 'emp\_id' 순서로 정렬한다.**

```sql
SELECT dept_id, LISTAGG(emp_name, ', ') WITHIN GROUP (ORDER BY emp_id) AS employee_names
FROM employees
GROUP BY dept_id;
```

**결과는 아래처럼 출력이 된다.**

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft"><tbody><tr><td style="width: 50%;"><b><span style="font-family: 'Noto Serif KR';">dept_id</span></b></td><td style="width: 50%;"><b><span style="font-family: 'Noto Serif KR';">employee_names</span></b></td></tr><tr><td style="width: 50%;"><b><span style="font-family: 'Noto Serif KR';">1</span></b></td><td style="width: 50%;"><b><span style="font-family: 'Noto Serif KR';">Alice, Bob</span></b></td></tr><tr><td style="width: 50%;"><b><span style="font-family: 'Noto Serif KR';">2</span></b></td><td style="width: 50%;"><b><span style="font-family: 'Noto Serif KR';">Carol, David</span></b></td></tr></tbody></table>

**LISTAGG 함수를 사용하면 그룹화된 데이터를 원하는 형식으로 문자열로 만들 수 있다.**
