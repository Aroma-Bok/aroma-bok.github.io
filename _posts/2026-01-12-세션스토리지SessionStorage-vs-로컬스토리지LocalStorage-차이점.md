---
title: "세션스토리지(SessionStorage) vs 로컬스토리지(LocalStorage) 차이점"
date: 2026-01-12 13:09:12 +0900
categories: ["ETC", "IT Knowledge"]
tags: ["로컬스토리지(localstorage)", "새 창으로 사용자정보 유지", "새탭 로그인정보 유지", "세션스토리지(sessionstorage)", "세션스토리지(sessionstorage) vs 로컬스토리지(localstorage) 차이점"]
tistory_url: "https://aroma-bok.tistory.com/entry/%EC%84%B8%EC%85%98%EC%8A%A4%ED%86%A0%EB%A6%AC%EC%A7%80SessionStorage-vs-%EB%A1%9C%EC%BB%AC%EC%8A%A4%ED%86%A0%EB%A6%AC%EC%A7%80LocalStorage-%EC%B0%A8%EC%9D%B4%EC%A0%90"
---
**먼저 간단하게 요약하면, 세션스토리지(SessionStorage)는 '일회용 포스트잇' 같고, 로컬스토리지(LocalStorage)는 '지워지지 않는 메모장' 같다고 표현할 수 있을 거 같다.**  
  

**두 저장소 모두 브라우저 내부에 데이터를 저장하는 공간이지만, 데이터가 "언제까지 유지되는가"와  
"범위가 어디까지인가"에서 큰 차이가 있다.**

* * *

#### **개념도 (Storage Structure)**

**● 브라우저 내부는 아래와 같은 구조로 데이터를 관리한다.**  
  
**① 로컬스토리지 (LocalStorage)**  
**→ 도메인별로 할단된 영구적인 서랍장 (브라우저를 꺼도 유지)**  
**② 세션스토리지 (SessionStorage)**  
**→ 탭마다 할당된 임시 포스트잇 (탭을 닫으면 파기)**  
**③ 쿠키 (Cookie)**  
**→ 서버와 주고받는 작은 증표 (용량이 작고 만료일이 있음)**

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft"><tbody><tr><td style="width: 22.8682%;"><span style="font-family: 'Noto Serif KR';"><b>구분</b></span></td><td style="width: 36.8217%;"><span style="font-family: 'Noto Serif KR';"><b>세션스토리지 (SessionStorage)</b></span></td><td style="width: 40.31%;"><span style="font-family: 'Noto Serif KR';"><b>로컬스토리지 (LocalStorage)</b></span></td></tr><tr><td style="width: 22.8682%;"><span style="font-family: 'Noto Serif KR';"><b>유지 기간</b></span></td><td style="width: 36.8217%;"><span style="font-family: 'Noto Serif KR';"><b>탭을 닫으면 즉시 삭제됨</b></span></td><td style="width: 40.31%;"><span style="font-family: 'Noto Serif KR';"><b>직접 삭제하기 전까지 영구 저장</b></span></td></tr><tr><td style="width: 22.8682%;"><span style="font-family: 'Noto Serif KR';"><b>새 탭 오픈 시</b></span></td><td style="width: 36.8217%;"><span style="font-family: 'Noto Serif KR';"><b>데이터가 공유되지 않음 (새 탭은 새 세션)</b></span></td><td style="width: 40.31%;"><span style="font-family: 'Noto Serif KR';"><b>동일 도메인이면 데이터가 모두 공유됨</b></span></td></tr><tr><td style="width: 22.8682%;"><span style="font-family: 'Noto Serif KR';"><b>브라우저 종료</b></span></td><td style="width: 36.8217%;"><span style="font-family: 'Noto Serif KR';"><b>종료 시 데이터 증발</b></span></td><td style="width: 40.31%;"><span style="font-family: 'Noto Serif KR';"><b>다시 켜도 데이터 그대로 유지</b></span></td></tr><tr><td style="width: 22.8682%;"><span style="font-family: 'Noto Serif KR';"><b>주요 용도</b></span></td><td style="width: 36.8217%;"><span style="font-family: 'Noto Serif KR';"><b>입력 폼 임시 저장, 일시적인 상태 유지</b></span></td><td style="width: 40.31%;"><span style="font-family: 'Noto Serif KR';"><b>자동 로그인, 사용자 환경 설정 (다크모드 등)</b></span></td></tr></tbody></table>

#### **상세 차이점**

**1\. 데이터의 수명 (Expiration)**  
**● 세션스토리지**  
**→ 윈도우나 브라우저 탭을 닫는 순간 데이터가 사라진다. 새로고침 시에는 유지되지만, 주소창에 주소를 다시 치고 들어가거나 새 탭을 열면 데이터가 없다.**  
**● 로컬스토리지**  
**→ 사용자가 브라우저 캐시를 수동으로 삭제하거나, 개발자가 코드로 지우지 않는 한 유통기한이 없다. 컴퓨터를 껐다 켜도 남아있다.  
**

**  
2\. 데이터 공유 범위 (Storage Scope)**

**● 세션스토리지**  
**→ '탭 단위'로 관리된다. 예를 들어, A 탭에서 로그인 정보를 세션스토리지에 넣고 B 탭을 새로 열면, B 탭은 그 정보를 모른다.** **"새 탭의 열었을 때 로그인이 풀리는 현상"의 주범.**  
**● 로컬스토리지**  
**→ 도메인 단위로 관리된다. naver.com에서 저장한 로컬스토리지 데이터는 크롬에서 열린 모든 naver.com 탭들이 공유한다.**

**3\. 보안과 용량  
● 두 곳 모두 약 5MB 내외의 텍스트 데이터를 저장할 수 있다.  
● 주의할 점은, 두 저장소 모두 자바스크립트로 쉽게 접근 가능하므로 민감한 개인정보나 비밀번호 원문을  
저장해서는 안된다. (그래서 보통 암호화된 토큰이나 사용자 설정값 정도만 저장한다.)**
