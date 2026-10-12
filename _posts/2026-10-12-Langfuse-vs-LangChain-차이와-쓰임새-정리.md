---
title: "Langfuse vs LangChain - 이름은 비슷한데 하는 일이 다르다"
date: 2026-10-12 09:00:00 +0900
categories: ["AI & Data Engineering"]
tags: ["langfuse", "langchain", "langgraph", "langsmith", "llmops", "llm-application", "observability", "agent"]
---
### 들어가며

Langfuse와 LangChain은 이름도 비슷하고 같이 언급되는 경우가 많아서 비슷한 도구인 줄 알았다. 정리해보니 **LangChain은 LLM 애플리케이션을 "만드는" 도구**이고, **Langfuse는 만든 애플리케이션이 "어떻게 동작했는지 기록하고 평가하는" 도구**였다. 경쟁 관계가 아니라 **같이 쓰는 관계**다.

(Langfuse 자체는 [Langfuse와 LLM 관측성 정리](/posts/Langfuse와-LLM-관측성-정리-LLMOps를-이해하기-위한-개념들/) 글에 자세히 정리해뒀다.)

### 1\. 한 줄 비교

| | LangChain | Langfuse |
|---|---|---|
| 한 줄 정의 | LLM 애플리케이션 **개발 프레임워크** | LLM 애플리케이션 **관측·평가 플랫폼** |
| 하는 일 | 모델 호출, 프롬프트, 검색, 도구, 에이전트를 **코드로 조립** | 실행 과정을 **기록**하고, 품질을 **평가**하고, 프롬프트 **버전을 관리** |
| 형태 | 내 코드에 import하는 **라이브러리** (Python, JS) | 별도로 띄우는 **서버 + 웹 화면** (SDK로 연결) |
| 개발 단계 | 만들 때 | 만든 뒤 운영·개선할 때 (개발 중 디버깅에도 사용) |
| 없으면? | 모든 연결 코드를 직접 짜야 함 | 문제가 생겨도 어디서 왜 생겼는지 찾기 어려움 |

**비유**: LangChain은 **공구 세트와 조립 설명서**, Langfuse는 완성된 기계에 달아두는 **블랙박스 + 품질 검사실**이다.

### 2\. LangChain - 만드는 도구

#### 왜 필요한가

LLM 애플리케이션을 직접 만들면 매번 비슷한 코드를 짜게 된다.

-   모델마다 다른 API 형식 맞추기
-   프롬프트 템플릿에 변수 채우기
-   문서를 잘라서(chunk) 임베딩하고 벡터 DB에 넣고 검색하기
-   검색 결과를 프롬프트에 끼워 넣기
-   LLM이 도구(API, 계산기, DB 조회)를 부르게 하기
-   여러 단계를 순서대로, 혹은 조건에 따라 실행하기

LangChain은 이런 부품을 **미리 만들어 두고, 레고처럼 연결**하게 해준다.

#### 주요 부품

| 부품 | 하는 일 |
|---|---|
| **Model** | OpenAI, Anthropic, 사내 모델 등 여러 LLM을 같은 방식으로 호출 |
| **Prompt Template** | 변수가 들어가는 프롬프트 틀 |
| **Document Loader / Text Splitter** | PDF·웹페이지 등을 읽고 잘게 자름 |
| **Embedding / Vector Store / Retriever** | 임베딩하고 벡터 DB에 저장하고 검색 (RAG의 재료) |
| **Tool** | LLM이 호출할 수 있는 외부 기능 |
| **Chain** | 부품을 순서대로 연결한 흐름 (프롬프트 → 모델 → 결과 파서) |
| **Agent** | LLM이 스스로 어떤 도구를 쓸지 판단하며 진행 |

#### 간단한 예

```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate

prompt = ChatPromptTemplate.from_template(
    "다음 문서를 근거로만 답하라.\n문서: {context}\n질문: {question}"
)
model = ChatOpenAI(model="gpt-4o-mini")

chain = prompt | model          # 프롬프트 → 모델을 연결
answer = chain.invoke({"context": "...", "question": "연차는 며칠 이월되나요?"})
```

#### 같은 회사의 형제 도구

LangChain 회사는 여러 도구를 같이 만든다. 이름이 비슷해서 헷갈리기 쉽다.

| 도구 | 역할 |
|---|---|
| **LangChain** | LLM 앱 부품 라이브러리 (기본 조립) |
| **LangGraph** | 상태가 있는 **복잡한 에이전트 흐름**을 그래프(노드·엣지)로 만드는 라이브러리. 분기, 반복, 사람 승인 대기 등 |
| **LangSmith** | LangChain 회사의 **관측·평가 플랫폼** |

여기서 중요한 점: **Langfuse와 직접 비교 대상은 LangChain이 아니라 LangSmith**다. 둘 다 "만든 앱을 기록하고 평가하는" 도구다.

### 3\. Langfuse - 기록하고 평가하는 도구

Langfuse는 앱을 만들어 주지 않는다. 대신 앱이 돌아갈 때,

-   **Trace**: 요청 1건이 어떤 단계를 거쳤는지 (검색 → 생성 → 검증)
-   **Generation**: LLM 호출마다 프롬프트, 응답, 토큰, 지연, 비용
-   **Score**: 그 결과에 대한 평가 점수 (AI 채점, 사람 판정, 사용자 피드백)
-   **Prompt 버전**: 어떤 버전의 프롬프트였는지
-   **Dataset / Experiment**: 같은 평가 데이터로 버전끼리 비교

를 남기고 보여준다.

### 4\. 같이 쓰는 모습

LangChain으로 만든 체인에 Langfuse의 콜백 핸들러를 끼우면, 체인 실행이 자동으로 Langfuse에 기록된다.

```python
from langfuse.langchain import CallbackHandler

langfuse_handler = CallbackHandler()

answer = chain.invoke(
    {"context": "...", "question": "연차는 며칠 이월되나요?"},
    config={"callbacks": [langfuse_handler]},   # 이 한 줄로 기록 연결
)
```

-   공식 문서 기준 **LangGraph도 같은 방식**으로 핸들러를 넘기면 된다
-   결과: Langfuse 화면에서 이 요청의 프롬프트 조립 → 모델 호출 → 응답이 한 Trace로 보인다

```
[개발]  LangChain / LangGraph 로 앱을 만든다
            │ 실행할 때마다
            ▼
[관측]  Langfuse 에 Trace·비용·지연이 쌓인다
            │
            ▼
[개선]  점수가 낮은 Trace를 찾아 → 프롬프트·검색 설정 수정
            → 같은 Dataset으로 실험 비교 → 나아졌으면 배포
```

### 5\. 노코드 에이전트 빌더는 어디에 들어가나

노코드 에이전트 빌더(워크플로 엔진)는 **LangChain·LangGraph와 같은 칸**에 들어간다. 둘 다 "만들고 실행하는" 도구다. 차이는 **코드로 조립하느냐, 화면에서 노드를 연결하느냐**다.

| 칸 | 코드 방식 | 노코드 방식 |
|---|---|---|
| **만들기·실행** | LangChain, LangGraph | 노코드 에이전트 빌더 (워크플로를 화면에서 구성) |
| **기록·평가** | Langfuse, LangSmith | Langfuse, LangSmith (빌더와 연동) |

그래서 "에이전트 빌더와 Langfuse를 통합한다"는 말은, **빌더가 실행한 각 노드의 기록을 Langfuse로 보내서 관측·평가한다**는 뜻이다. LangChain에 콜백 핸들러를 끼우는 것과 같은 구조를, 빌더 쪽에서는 OpenTelemetry나 SDK 연동으로 구현하는 것이다.

### 6\. 언제 무엇을 쓰나

| 상황 | 쓰는 것 |
|---|---|
| LLM 앱을 **코드로 빠르게 만들고 싶다** | LangChain |
| 분기·반복·사람 승인이 있는 **복잡한 에이전트**를 코드로 만들고 싶다 | LangGraph |
| 개발자 없이 **화면에서 워크플로를 구성**하고 싶다 | 노코드 에이전트 빌더 |
| 만든 앱이 **어디서 틀리고 느린지** 보고 싶다 | Langfuse (또는 LangSmith) |
| 프롬프트를 바꿨을 때 **좋아졌는지 숫자로 비교**하고 싶다 | Langfuse (또는 LangSmith) |
| 폐쇄망·사내망에 **직접 설치해서 기록을 밖으로 안 보내고** 싶다 | Langfuse (오픈소스, self-hosting) |

참고로 LangChain 없이도 Langfuse를 쓸 수 있고, Langfuse 없이도 LangChain을 쓸 수 있다. **둘은 독립적이고, 필요할 때 연결해서 쓴다.**

### 7\. 헷갈리지 않기 위한 정리

-   **LangChain ≠ Langfuse**: 만드는 도구 vs 기록·평가하는 도구
-   **Langfuse의 경쟁 상대는 LangSmith**: 둘 다 관측·평가 플랫폼
-   **LangGraph는 LangChain의 확장**: 복잡한 에이전트 흐름용
-   **노코드 빌더는 LangChain 쪽 칸**: 만들고 실행하는 역할을 화면으로 함

### 마무리

한 줄로 줄이면, **LangChain은 LLM 앱을 "조립"하고, Langfuse는 그 앱이 "어떻게 동작했는지 기록하고 채점"한다.** 이름이 비슷해서 같은 종류처럼 보이지만, 개발 → 운영 → 개선 흐름에서 맡는 칸이 다르다.
