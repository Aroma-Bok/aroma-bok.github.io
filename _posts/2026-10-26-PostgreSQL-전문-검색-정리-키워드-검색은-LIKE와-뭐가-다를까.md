---
title: "PostgreSQL 전문 검색 정리 - 키워드 검색은 LIKE와 뭐가 다를까 (어간, tsvector, ts_rank)"
date: 2026-10-26 09:00:00 +0900
categories: ["Solution & Tools", "DB"]
tags: ["postgresql", "full-text-search", "tsvector", "tsquery", "ts_rank", "stemming", "like", "gin-index", "keyword-search", "sql", "pgvector", "vector-search", "embedding"]
tistory_url: "https://aroma-bok.tistory.com/entry/PostgreSQL-%EC%A0%84%EB%AC%B8-%EA%B2%80%EC%83%89-%EC%A0%95%EB%A6%AC-%ED%82%A4%EC%9B%8C%EB%93%9C-%EA%B2%80%EC%83%89%EC%9D%80-LIKE%EC%99%80-%EB%AD%90%EA%B0%80-%EB%8B%A4%EB%A5%BC%EA%B9%8C-%EC%96%B4%EA%B0%84-tsvector-tsrank"
---
### 들어가며

"키워드 검색"이라고 하면 보통 `active`라고 입력하면 `active`라는 글자가 들어 있는 글만 찾아 준다고 생각한다. 나도 그렇게 이해하고 있었다. 그런데 PostgreSQL의 전문 검색(Full Text Search)으로 `active`를 조회했더니 `activities`, `activity`가 들어 있는 글까지 같이 나왔다. LIKE 검색이 아닌데 어떻게 이런 결과가 나오는지, 그 내부 동작을 정리해본다.

### 1. 먼저 결론

- **LIKE 검색**은 글자가 들어 있는지만 본다. `%active%`는 `active`라는 글자 덩어리가 있으면 찾는다.
- **전문 검색**은 문장을 단어로 쪼개고, 각 단어를 **어간(stem)** 으로 줄여서 저장해 둔 뒤, 검색어도 같은 방식으로 줄여서 비교한다.
- 그래서 전문 검색은 `active`, `activity`, `activities`를 같은 단어로 취급한다.
- 둘 다 **문자열 기반의 키워드 검색**이다. 문맥이나 동의어를 이해하는 의미 검색(벡터 검색)과는 다르다.

### 2. 어간(stem)이란

단어의 변형(복수형, 시제, 파생형)을 걷어 내고 남은 **공통 뿌리 부분**이다. 영어 단어는 `active`, `activity`, `activities`, `actively`처럼 형태가 바뀌는데, 검색할 때는 이것들을 같은 단어로 보고 싶을 때가 많다. 그래서 규칙에 따라 끝부분을 잘라 같은 뿌리로 모은다.

- `active`, `activity`, `activities` → `activ`
- `running` → `run`
- `clocks` → `clock`

알아둘 점이 두 가지 있다.

- **어간은 사전 형태가 아니다.** 규칙으로 자른 결과라서 `additional`이 `addit`처럼 어색하게 보일 수 있다. 검색어도 같은 규칙으로 줄이기 때문에 비교하는 데는 문제가 없다.
- **불규칙 변화는 처리하지 못한다.** `went`는 `go`가 되지 않고 `went` 그대로이고, `happier`도 `happier` 그대로다. 기본형을 찾아 주는 표제어 추출(lemmatization)이 아니라 규칙 기반 자르기(stemming)이기 때문이다.

### 3. tsvector: 문서를 단어 목록으로 바꿔서 저장

`to_tsvector`는 문장을 **소문자 어간 + 등장 위치** 목록으로 바꾼다.

```sql
SELECT to_tsvector('english',
  'The active clocks were additional activities, and the clock is active.');
-- 'activ':2,6,11 'addit':5 'clock':3,9
```

이 결과는 이렇게 읽는다.

- **`'activ':2,6,11`**: 어간 `activ`가 2번째, 6번째, 11번째 단어 자리에서 나왔다. `active`(2번째), `activities`(6번째), `active`(11번째)가 하나로 합쳐졌다.
- **숫자는 단어 위치**이지 테이블의 행 번호가 아니다.
- **같은 어간은 한 번만 적고 위치를 모아 둔다.** `clocks`(3번째)와 `clock`(9번째)가 `'clock':3,9`가 되었다.
- **`the`, `were`, `and`, `is` 같은 불용어(stop word)는 빠진다.** 다만 위치 번호는 불용어까지 센 값이라 `additional`이 5번째로 적힌다.

테이블에 넣어 둘 때는 생성 컬럼으로 만들면 편하다. 본문만 넣으면 DB가 알아서 채우고, 본문을 수정하면 다시 계산한다.

```sql
CREATE TABLE doc (
    id         bigserial PRIMARY KEY,
    body       text NOT NULL,
    search_vec tsvector GENERATED ALWAYS AS (to_tsvector('english', body)) STORED
);

CREATE INDEX ix_doc_search ON doc USING gin (search_vec);
```

### 4. tsquery: 검색어도 같은 방식으로 변환

검색어를 `websearch_to_tsquery`로 바꾸면 같은 규칙으로 어간이 된다.

```sql
SELECT websearch_to_tsquery('english', 'active');               -- 'activ'
SELECT websearch_to_tsquery('english', '"physical activity"');  -- 'physic' <-> 'activ'
SELECT websearch_to_tsquery('english', 'walking OR swimming');  -- 'walk' | 'swim'
SELECT websearch_to_tsquery('english', 'activity -inactive');   -- 'activ' & !'inact'
```

- 단어를 띄어 쓰면 **AND**다. 모두 들어 있어야 한다.
- 큰따옴표로 묶으면 **연속한 구절**(`<->`)이다.
- `OR`는 **OR**, 앞에 `-`를 붙이면 **제외**다.

문서 쪽과 검색어 쪽이 **같은 규칙(`'english'`)** 으로 변환되어야 서로 맞는다. 문서는 `english`, 검색어는 다른 설정으로 만들면 어긋난다.

### 5. 매칭과 점수: @@ 와 ts_rank

```sql
SELECT id, ts_rank(search_vec, q) AS score
FROM doc, websearch_to_tsquery('english', 'active') q
WHERE search_vec @@ q
ORDER BY score DESC;
```

- **`websearch_to_tsquery(...) q`**: 검색어를 질의로 바꾸고 `q`라는 이름을 붙여서 아래에서 재사용한다. 변환만 할 뿐 조회는 하지 않는다.
- **`search_vec @@ q`**: 이 단어 목록이 질의와 맞는지 true/false로 판단한다. WHERE에 쓰면 맞는 행만 남기고, GIN 인덱스도 이 조건에서 쓰인다.
- **`ts_rank(search_vec, q)`**: 맞는 행에 점수를 매긴다. 걸러내지는 않고 정렬용 숫자만 돌려준다.

### 6. 직접 비교해 보기

네 개의 짧은 문장으로 LIKE와 전문 검색의 차이를 확인했다.

```sql
WITH docs(id, body) AS (VALUES
 (1, 'Active learning improves memory.'),
 (2, 'The club runs outdoor activities every week.'),
 (3, 'He felt inactive after the long trip.'),
 (4, 'Physical activity is good for health. Activities such as walking and swimming help.'))
SELECT id,
       body LIKE '%active%' AS like_active,
       to_tsvector('english', body) @@ websearch_to_tsquery('english', 'active') AS fts_active,
       round(ts_rank(to_tsvector('english', body),
                     websearch_to_tsquery('english', 'active'))::numeric, 4) AS score
FROM docs ORDER BY id;
```

| id | 문장 | LIKE '%active%' | 전문 검색 | score |
|---|---|---|---|---|
| 1 | Active learning improves memory. | false | true | 0.0608 |
| 2 | The club runs outdoor activities every week. | false | true | 0.0608 |
| 3 | He felt inactive after the long trip. | **true** | false | 0.0000 |
| 4 | Physical activity ... Activities ... | false | true | 0.0760 |

- **1번**은 LIKE가 못 찾는다. `Active`는 대문자라서 `active`와 글자가 다르다(LIKE는 대소문자를 구분한다). 전문 검색은 소문자로 바꾸기 때문에 찾는다.
- **2번, 4번**은 `activities`, `activity`라서 LIKE가 못 찾는다. 전문 검색은 어간이 같아서 찾는다.
- **3번**은 반대다. `inactive` 안에 `active`라는 글자가 들어 있어서 LIKE는 찾지만, 전문 검색에서는 `inactive`가 `inact`라는 다른 단어라서 찾지 않는다.

LIKE로 하려던 일에서 오히려 원하지 않는 결과(`inactive`)가 나오고 원하는 결과(`activities`)가 빠진다. 단어 단위로 검색하고 싶다면 전문 검색이 맞다.

### 7. ts_rank 점수는 어떻게 계산될까

위 표에서 4번만 점수가 높다. `activ`가 두 번 나오기 때문이다. 검색어가 한 개일 때 기본 `ts_rank`는 **출현 횟수**만 본다.

- 어간에 별도 가중치를 주지 않으면 모두 D 등급이고 가중치는 0.1이다.
- 한 번 나오면 `0.1 / 1.6449 = 0.0608`이다. (1.6449는 π²/6)
- 두 번 나오면 둘째 번은 1/4로 줄여서 더한다. `(0.1 + 0.1/4) / 1.6449 = 0.0760`이다.
- 세 번째는 1/9, 네 번째는 1/16으로 줄어든다. 많이 나와도 점수는 완만하게만 오른다.

실제 결과값과 위 계산이 일치하는 것을 확인했다. 이 점수에서 알아둘 점은 다음과 같다.

- **문서 길이는 반영하지 않는다.** 긴 글도 불이익이 없다. 길이를 반영하려면 `ts_rank(search_vec, q, 1)`처럼 세 번째 인자를 준다.
- **점수 자체에 절대적인 의미는 없다.** 같은 검색어의 결과끼리 순서를 정하는 용도이고, 다른 검색어의 점수와 비교하면 안 된다.
- **단어의 희귀성은 모른다.** 흔한 단어와 드문 단어를 똑같이 취급한다. 그런 점수가 필요하면 BM25 같은 다른 방식을 써야 한다.
- 점수가 같으면 순서가 정해지지 않으므로 `ORDER BY score DESC, id`처럼 보조 정렬을 두는 편이 좋다.

### 8. 키워드 검색의 한계

- `english` 설정은 **영어 전용**이다. 한국어 문장에는 이 규칙이 맞지 않는다. 한국어는 글자 조각 유사도(`pg_trgm`)나 별도의 형태소 분석이 필요하다.
- **동의어와 의미는 못 잡는다.** `happy`로 검색해도 `joyful`만 있는 글은 찾지 못한다. 어간이 같아야 같은 단어로 보기 때문이다. 이 한계를 메우는 것이 다음에 나오는 벡터 검색이다.

### 9. 벡터 검색은 무엇이 다를까

키워드 검색이 **단어가 들어 있는지**를 비교한다면, 벡터 검색은 **의미가 가까운지**를 비교한다.

- **임베딩(embedding)**: 문장을 **의미를 담은 숫자 배열**로 바꾼 것이다. 임베딩 모델에 문장을 넣으면 `[0.12, -0.03, 0.88, ...]`처럼 수백~수천 개의 숫자가 나온다. 뜻이 비슷한 문장은 비슷한 숫자 배열이 나오도록 학습된 모델이다.
- **벡터 검색**: 문서를 미리 임베딩해서 저장해 두고, 검색어도 같은 모델로 임베딩한 뒤, 두 배열이 **얼마나 가까운지**(거리, 유사도)로 순위를 매긴다.

흐름은 이렇게 두 단계다.

1. **저장할 때(한 번)**: 문서 본문을 임베딩 모델에 넣어 숫자 배열을 얻고, 본문과 함께 저장한다.
2. **검색할 때(매번)**: 검색어를 **같은 모델**로 임베딩하고, 저장된 배열들과 거리를 비교해 가까운 순으로 가져온다.

키워드 검색에서 문서와 검색어를 **같은 규칙**(`'english'`)으로 변환해야 했던 것과 같다. 벡터 검색에서는 **같은 임베딩 모델**을 써야 한다. 모델이 다르면 숫자의 의미가 달라서 비교할 수 없다.

#### 원리를 SQL로 확인해 보기

임베딩 모델 없이 원리만 보려고, 의미를 3개의 숫자로 손으로 만든 장난감 벡터를 썼다. 실제 임베딩은 이렇게 사람이 만들 수 없고 모델이 만든다. 비교 방식인 **코사인 유사도**(두 배열이 같은 방향을 향하는 정도, 1에 가까울수록 비슷함)를 일반 SQL로 계산했다.

```sql
WITH docs(id, body, v) AS (VALUES
 (1, 'She felt joyful today.',          ARRAY[0.90, 0.10, 0.05]::float8[]),
 (2, 'He was delighted with the gift.', ARRAY[0.85, 0.15, 0.10]),
 (3, 'The engine needs new oil.',       ARRAY[0.05, 0.10, 0.95]),
 (4, 'I am happy.',                     ARRAY[0.92, 0.08, 0.03])),
q(v) AS (VALUES (ARRAY[0.88, 0.12, 0.04]::float8[]))   -- 'happy'를 나타내는 장난감 벡터
SELECT d.id, d.body,
       round(((SELECT sum(a*b) FROM unnest(d.v, q.v) AS t(a,b)) /
        (sqrt((SELECT sum(a*a) FROM unnest(d.v) a)) *
         sqrt((SELECT sum(b*b) FROM unnest(q.v) b))))::numeric, 4) AS cosine_similarity
FROM docs d, q
ORDER BY cosine_similarity DESC;
```

| id | 문장 | 코사인 유사도 |
|---|---|---|
| 1 | She felt joyful today. | 0.9996 |
| 4 | I am happy. | 0.9987 |
| 2 | He was delighted with the gift. | 0.9968 |
| 3 | The engine needs new oil. | 0.1105 |

- `happy`라는 단어가 없는 `joyful`, `delighted` 문장이 가장 위에 온다. 키워드 검색으로는 찾을 수 없던 결과다.
- 뜻이 전혀 다른 `engine` 문장은 유사도가 낮다.
- 이 숫자는 설명용으로 직접 지정한 값이다. 실제 모델이 만든 임베딩의 결과가 아니다.

#### PostgreSQL에서는 pgvector 확장을 쓴다

PostgreSQL은 기본 기능만으로는 벡터를 효율적으로 저장하고 검색하지 못한다. 대신 **pgvector 확장**을 설치하면 `vector` 타입과 거리 연산자, 벡터 전용 인덱스를 쓸 수 있다. 아래는 pgvector 문서를 기준으로 정리한 사용 형태이고, 앞의 장난감 예제와 달리 직접 실행해 본 것은 아니다.

```sql
CREATE EXTENSION vector;

CREATE TABLE doc (
    id        bigserial PRIMARY KEY,
    body      text NOT NULL,
    embedding vector(1536)      -- 쓰는 임베딩 모델의 차원 수와 같아야 한다
);

-- 가까운 순 5건 (<=> 는 코사인 거리, 작을수록 비슷함)
SELECT id, body, embedding <=> $1 AS distance
FROM doc
ORDER BY embedding <=> $1
LIMIT 5;
```

- **`vector(n)`**: `n`개의 숫자를 담는 타입이다. 모델마다 차원 수가 다르므로 모델에 맞춰야 한다.
- **`<=>`**: 코사인 거리다. 이 외에 유클리드 거리 `<->`, 내적 `<#>`가 있다.
- **`$1`**: 검색어를 임베딩 모델에 넣어 얻은 배열이다. 이 변환은 DB가 아니라 **애플리케이션이 모델을 호출해서** 만들어 넘긴다.
- **인덱스**: 문서가 많아지면 전체를 다 비교하기 어려워서 근사 검색 인덱스(HNSW, IVFFlat)를 건다. 정확도를 조금 양보하는 대신 훨씬 빠르다.

설치는 환경에 따라 다르다. Linux는 패키지로 설치하는 경우가 많고, Windows는 공식 설치 파일이 아니라 Visual Studio C++ 빌드 도구로 `nmake`로 빌드하는 방법이 안내되어 있다. 그 뒤 `CREATE EXTENSION vector;`를 실행한다.

### 10. 키워드 검색 vs 벡터 검색

| | 키워드 검색 (`tsvector`) | 벡터 검색 (`pgvector`) |
|---|---|---|
| 비교 대상 | 단어(어간) | 임베딩 숫자 배열 |
| 저장되는 것 | `'activ':2,6` 같은 단어·위치 목록 | `[0.12, -0.03, ...]` 숫자 배열 |
| 만드는 법 | DB 함수가 규칙으로 변환 | 임베딩 모델을 호출해서 얻음 |
| 동의어 | 못 찾음 | 찾음 |
| 정확한 용어, 코드, 고유명사 | 잘 찾음 | 놓칠 수 있음 |
| 결과 설명 | 어떤 단어가 맞았는지 보임 | 왜 가까운지 설명하기 어려움 |
| 비용 | 낮음 | 임베딩 모델 호출 비용이 듦 |
| PostgreSQL | 기본 기능 | 확장 설치 필요 |

이름이 비슷한 `tsvector`와 pgvector의 `vector`는 **서로 다른 타입**이다. 전자는 **단어와 위치 목록**이고, 후자는 **임베딩 숫자 배열**이다.

둘은 장단점이 반대라서 **함께 쓰는 하이브리드 검색**이 많다. 키워드 검색 결과와 벡터 검색 결과를 합쳐서 순위를 정한다. 정확한 용어는 키워드가, 뜻이 비슷한 표현은 벡터가 메워 준다.

### 정리

- 전문 검색은 **어간으로 줄여서 저장하고, 검색어도 줄여서 비교**한다.
- `to_tsvector`는 문서를 어간 + 위치 목록으로, `websearch_to_tsquery`는 검색어를 질의로 바꾼다.
- `@@`는 걸러내고, `ts_rank`는 점수를 매기며, GIN 인덱스는 `어간 → 문서` 목록을 만들어 빠르게 찾게 한다.
- LIKE는 글자 일치, 전문 검색은 단어(어간) 일치다. 둘 다 문자열 기반이라 의미까지 이해하지는 못한다.
- 벡터 검색은 **문서와 검색어를 같은 임베딩 모델로 숫자 배열로 바꾸고, 거리가 가까운 순**으로 찾는다. 동의어와 의미를 잡지만 정확한 용어에는 약할 수 있다.
- PostgreSQL에서는 **pgvector 확장**으로 벡터 검색을 하고, 실무에서는 키워드와 벡터를 함께 쓰는 하이브리드가 많다.
