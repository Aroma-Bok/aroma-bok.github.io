---
title: "HashMap vs LinkedHashMap 차이점"
date: 2023-08-21 13:09:23 +0900
categories: ["ETC", "알고리즘"]
tags: ["hashmap", "hashmap linkedhashmap 차이점"]
tistory_url: "https://aroma-bok.tistory.com/entry/HashMap-vs-LinkedHashMap-%EC%B0%A8%EC%9D%B4%EC%A0%90"
---
**둘 다 Key-Value로 값을 저장하며, Key를 통해 Value를 검색하고 저장할 수 있다.**

**그러나 두 구현체 간에는 몇 가지 중요한 차이점이 있다.**

* * *

**순서 유지** 

-   **HashMap : 요소의 순서를 보장하지 않는다. Key들의 순서는 Hash 함수에 의해 결정되므로 예측할 수 없다.**
-   **LinkedHashMap : 요소들의 삽입 순서를 유지한다.**

**성능**

-   **HashMap : Hash 함수를 사용하여 데이터를 저장하므로 Key의 HashCode에 따라 데이터가 저장되는 위치를 결정한다. 일반적으로 매우 빠른 검색 및 삽입 성능을 가진다.**
-   **LinkedHashMap : 추가적인 Linked List를 유지하기 때문에 데이터의 삽입 및 삭제가 조금 더 느릴 수 있다. 그러나 순회 시에는 삽입 순서대로 데이터에 접근할 수 있다.**

**메모리 사용량**

-   **HashMap : LinkedHashMap보다 메모리 사용량이 적을 수 있다.**
-   **LinkedHashMap : Linked List를 유지하기 때문에 메모리 사용이 더 크다.**

**활용예시**

-   **HashMap : 순서가 중요하지 않고 검색 및 삽입 성능이 중요한 경우**
-   **LinkedHashMap : 순서를 유지하며 순회나 특정 순서대로 데이터에 접근해야 하는 경우**
