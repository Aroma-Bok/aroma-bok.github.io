---
title: "[ORACLE] COALESCE vs NVL 개념 및 차이점"
date: 2023-08-28 10:19:19 +0900
categories: ["Solution & Tools", "DB"]
tags: ["coalesce", "coalesce vs nvl 차이", "nvl"]
tistory_url: "https://aroma-bok.tistory.com/entry/ORACLE-COALESCE-vs-NVL-%EA%B0%9C%EB%85%90-%EB%B0%8F-%EC%B0%A8%EC%9D%B4%EC%A0%90"
---
**NULL 값에 대해서 NVL만 사용했었는데, COALESCE라는 함수를 알게 되면서 차이점과 개념을 정리해 보았다.**

* * *

**먼저 COALESCE와 NVL은 모두 NULL 값을 처리하는 함수이지만, 차이점은 사용 가능한 인자의 수와 동작 방식이다.**

**COALESCE**

-   **구문 : COALESCE(value1, value2, value3,... valueN)**
-   **COALESCE 함수는 여러 개의 인자를 받을 수 있다.**
-   **인자 중에서 첫 번째 NON-NULL 값을 반환한다.**
-   **인자를 왼쪽 -> 오른쪽 순서대로 확인하며 NON-NULL 값을 찾으며 바로 반환한다.**
-   **모든 인자가 NULL 인 경우에는 NULL을 반환한다.**

```sql
SELECT COALESCE(null, 1, 2, 3);  -- 결과: 1
SELECT COALESCE(null, null, null);  -- 결과: NULL
```

* * *

**NVL**

-   **구문 : NVL(value1, value2)**
-   **NVL 함수는 두 개의 인자를 받는다.**
-   **첫 번째 인자인 value1이 NULL이 아니면 value1을 반환하고, NULL이면 value2를 반환**

```sql
SELECT NVL(null, 1);  -- 결과: 1
SELECT NVL(null, 'default');  -- 결과: 'default'
```

* * *

**COALESCE는 여러 개의 인자를 받을 수 있고, 첫 번째 NON-NULL 값을 반환하는 반면,**

**NVL은 두 개의 인자를 받고 첫 번째 값이 NULL이면 두 번째 값을 반환한다.**

**선택적으로 사용할 수 있는 상황에 따라 어떤 함수를 사용할지 선택하면 된다.**
