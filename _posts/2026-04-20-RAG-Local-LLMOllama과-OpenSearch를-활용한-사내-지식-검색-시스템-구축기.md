---
title: "[RAG] Local LLM(Ollama)과 OpenSearch를 활용한 지식 검색 시스템 구축기"
date: 2026-04-20 14:16:57 +0900
categories: ["AI & Data Engineering"]
tags: ["java langchain4j", "java llm 연동", "java ollama", "java opensearch 활용", "java rag 구축", "langchain4j"]
tistory_url: "https://aroma-bok.tistory.com/entry/RAG-Local-LLMOllama%EA%B3%BC-OpenSearch%EB%A5%BC-%ED%99%9C%EC%9A%A9%ED%95%9C-%EC%82%AC%EB%82%B4-%EC%A7%80%EC%8B%9D-%EA%B2%80%EC%83%89-%EC%8B%9C%EC%8A%A4%ED%85%9C-%EA%B5%AC%EC%B6%95%EA%B8%B0"
---
최근 기업 보안 이슈로 인해 외부 API(OpenAI 등)를 쓰지 않고 로컬에서 LLM을 구동하려는 수요가 많습니다.

오늘은 **S3(MinIO), OpenSearch, Ollama**를 활용해 자바 환경에서 간단한 RAG 시스템을 구축한 과정을 공유합니다.

* * *

![](/assets/img/posts/332/1.png)

### 1\. 전체 아키텍처

본 시스템은 **데이터 수집 → 벡터화 → 저장 → 검색 → 답변 생성**의 5단계 흐름을 가집니다.

-   **Storage**: MinIO (S3 compatible)
-   **Vector DB**: OpenSearch
-   **LLM Engine**: Ollama (Llama 3)
-   **Framework**: LangChain4j

* * *

### 2\. 핵심 기술 스택 및 역할

<table style="border-collapse: collapse; width: 100%;" border="1" data-path-to-node="10" data-ke-align="alignLeft"><tbody><tr><td><b>기술</b></td><td><b>역할</b></td><td><b>비고</b></td></tr></tbody><tbody><tr><td><span data-path-to-node="10,1,0,0"><b data-index-in-node="0" data-path-to-node="10,1,0,0">MinIO</b></span></td><td><span data-path-to-node="10,1,1,0">원본 데이터(txt) 저장소</span></td><td><span data-path-to-node="10,1,2,0">S3 호환 로컬 스토리지</span></td></tr><tr><td><span data-path-to-node="10,2,0,0"><b data-index-in-node="0" data-path-to-node="10,2,0,0">OpenSearch</b></span></td><td><span data-path-to-node="10,2,1,0">벡터 데이터 및 텍스트 검색</span></td><td><span data-path-to-node="10,2,2,0">지식 창고 역할</span></td></tr><tr><td><span data-path-to-node="10,3,0,0"><b data-index-in-node="0" data-path-to-node="10,3,0,0">Ollama (Llama3)</b></span></td><td><span data-path-to-node="10,3,1,0">자연어 추론 및 답변 생성</span></td><td><span data-path-to-node="10,3,2,0">로컬 구동 LLM</span></td></tr><tr><td><span data-path-to-node="10,4,0,0"><b data-index-in-node="0" data-path-to-node="10,4,0,0">LangChain4j</b></span></td><td><span data-path-to-node="10,4,1,0">각 기술 스택을 연결하는 프레임워크</span></td><td><span data-path-to-node="10,4,2,0">Java 기반 AI 라이브러리</span></td></tr></tbody></table>

* * *

### 3\. 주요 구현 코드

#### ✅ S3(MinIO)에서 다중 파일 로드하기

단일 파일이 아닌 버킷 내의 모든 파일을 순회하며 읽어오는 방식입니다.

```nix
ListObjectsV2Request listRequest = ListObjectsV2Request.builder().bucket("my-docs").build();
ListObjectsV2Response listResponse = s3.listObjectsV2(listRequest);

StringBuilder allS3Data = new StringBuilder();
for (S3Object s3Object : listResponse.contents()) {
    ResponseBytes<GetObjectResponse> objectBytes = s3.getObjectAsBytes(
            GetObjectRequest.builder().bucket("my-docs").key(s3Object.key()).build());
    allS3Data.append(objectBytes.asUtf8String()).append("\n");
}
String s3Data = allS3Data.toString();
```

![](/assets/img/posts/332/2.png)

*A에 대한 내용 학습*

![](/assets/img/posts/332/3.png)

*B에 대한 질문*

#### ✅ OpenSearch & Ollama 설정

LangChain4j를 이용해 인덱스를 지정하고 로컬 LLM인 Llama3를 연결합니다.

```reasonml
var embeddingStore = OpenSearchEmbeddingStore.builder()
        .serverUrl("http://localhost:9200")
        .indexName("s3-knowledge") // 실제 생성된 인덱스명 확인 필수
        .build();

ChatLanguageModel model = OllamaChatModel.builder()
        .baseUrl("http://localhost:11434")
        .modelName("llama3")
        .build();
```

* * *

### 4\. 트러블슈팅

-   **GradleWorkerMain 에러**: 로컬 리소스(VRAM/RAM) 부족 시 Gradle 데몬이 꼬이는 현상. gradle.properties에 org.gradle.daemon=false를 설정하여 해결했습니다.
-   **OpenSearch Security**: 2.12 버전 이후 보안 설정이 필수입니다. 로컬 테스트 시 plugins.security.disabled=true 옵션으로 간소화할 수 있습니다.
-   **VRAM의 중요성**: LLM 연산은 GPU의 VRAM을 사용합니다. 사양이 낮을 경우 phi3 같은 경량 모델을 쓰거나 답변 속도를 인내해야 합니다.

* * *

### 5\. 마치며

이번 프로젝트를 통해 보안이 중요한 사내 문서 검색 시스템의 초석을 다질 수 있었습니다.

로컬 환경이라 초기 설정은 까다롭지만, 한 번 구축해두면 비용 걱정 없이 무제한으로 테스트할 수 있다는 점이 큰 매력입니다.
