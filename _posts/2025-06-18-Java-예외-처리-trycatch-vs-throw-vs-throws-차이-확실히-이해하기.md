---
title: "[Java] 예외 처리 try~catch vs throw vs throws 차이 확실히 이해하기"
date: 2025-06-18 17:18:50 +0900
categories: ["Language", "Java"]
tags: ["throw throws 차이점", "try~catch throws throw 차이점", "익센션 처리 방법", "자바 예외종류", "코드 예외처리 방법"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-%EC%98%88%EC%99%B8-%EC%B2%98%EB%A6%AC-trycatch-vs-throw-vs-throws-%EC%B0%A8%EC%9D%B4-%ED%99%95%EC%8B%A4%ED%9E%88-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0"
---
#### **예외(Exception)란?**

-   **프로그램 실행 중 발생하는 \*\*비정상적인 상황(오류)\*\*을 객체로 표현한 것**  
    **예: 파일 없음, DB 연결 실패, null 참조, 인덱스 초과 등**

![](/assets/img/posts/317/1.png)

#### **예외의 종류 (가장 중요한 구분)**

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft"><tbody><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>구분</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR'; color: #ee2323;"><b>Checked Exception</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR'; color: #ee2323;"><b>Unchecked Exception</b></span></td></tr><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>검사 시점</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>컴파일 시점</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>런타임 시점</b></span></td></tr><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>예외 처리 강제 여부</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>✅ <span style="color: #ee2323;">반드시 처리 (throws or try-catch)</span></b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>❌ 선택 사항</b></span></td></tr><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>대표 클래스</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>IOException, SQLException,</b></span><br><span style="font-family: 'Noto Serif KR';"><b>ParseException</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b>NullPointerException,</b></span><br><span style="font-family: 'Noto Serif KR';"><b>IllegalArgumentException,</b></span><br><span style="font-family: 'Noto Serif KR';"><b>IndexOutOfBoundsException</b></span></td></tr><tr><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>상속 계열</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR';"><b><span style="color: #ee2323;">Exception</span><br>(단, RuntimeException 제외)</b></span></td><td style="width: 33.3333%;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>RuntimeException 및 하위 클래스</b></span></td></tr></tbody></table>

#### **핵심 키워드**

<table style="border-collapse: collapse; width: 100%; height: 105px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 22px;"><td style="width: 25%; height: 22px;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>키워드</b></span></td><td style="width: 25%; height: 22px;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>역할</b></span></td><td style="width: 18.3721%; height: 22px;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>위치</b></span></td><td style="width: 31.6279%; height: 22px;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>예</b></span></td></tr><tr style="height: 22px;"><td style="width: 25%; height: 22px;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>throw</b></span></td><td style="width: 25%; height: 22px;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>예외 직접 발생</b></span></td><td style="width: 18.3721%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>메서드 내부</b></span></td><td style="width: 31.6279%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>throw new IllegalArgumentException("에러")</b></span></td></tr><tr style="height: 22px;"><td style="width: 25%; height: 22px;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>throws</b></span></td><td style="width: 25%; height: 22px;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>예외 위임 선언</b></span></td><td style="width: 18.3721%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>메서드 선언부</b></span></td><td style="width: 31.6279%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>void run() throws IOException</b></span></td></tr><tr style="height: 22px;"><td style="width: 25%; height: 22px;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>try~catch</b></span></td><td style="width: 25%; height: 22px;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>예외 직접 처리</b></span></td><td style="width: 18.3721%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>메서드 내부</b></span></td><td style="width: 31.6279%; height: 22px;"><span style="font-family: 'Noto Serif KR';"><b>try { ... } catch (IOException e) { ... }</b></span></td></tr><tr style="height: 17px;"><td style="width: 25%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>finally</b></span></td><td style="width: 25%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>무조건 실행 (리소스 정리 등)</b></span></td><td style="width: 18.3721%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>try~catch 끝에</b></span></td><td style="width: 31.6279%; height: 17px;"><span style="font-family: 'Noto Serif KR';"><b>finally { conn.close(); }</b></span></td></tr></tbody></table>

#### **throw vs throws vs try-catch 차이**

<table style="border-collapse: collapse; width: 100%; height: 148px;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 22px;"><td style="width: 25%; height: 22px;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>항목</b></span></td><td style="width: 25%; height: 22px;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR'; color: #ee2323;"><b>throw</b></span></td><td style="width: 25%; height: 22px;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR'; color: #ee2323;"><b>throws</b></span></td><td style="width: 25%; height: 22px;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR'; color: #ee2323;"><b>try~catch</b></span></td></tr><tr style="height: 21px;"><td style="width: 25%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>역할</b></span></td><td style="width: 25%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>예외를 직접 발생</b></span></td><td style="width: 25%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>예외를 선언하여 위임</b></span></td><td style="width: 25%; height: 21px;"><span style="font-family: 'Noto Serif KR'; color: #ee2323;"><b>예외를 직접 처리</b></span></td></tr><tr style="height: 42px;"><td style="width: 25%; height: 42px;"><span style="font-family: 'Noto Serif KR';"><b>예외 객체 필요 여부</b></span></td><td style="width: 25%; height: 42px;"><span style="font-family: 'Noto Serif KR';"><b>✅ 필요 (new Exception(...))</b></span></td><td style="width: 25%; height: 42px;"><span style="font-family: 'Noto Serif KR';"><b>❌ 필요 없음</b></span></td><td style="width: 25%; height: 42px;"><span style="font-family: 'Noto Serif KR';"><b>❌ 필요 없음</b></span></td></tr><tr style="height: 21px;"><td style="width: 25%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>위치</b></span></td><td style="width: 25%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>메서드 내부</b></span></td><td style="width: 25%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>메서드 선언부</b></span></td><td style="width: 25%; height: 21px;"><span style="font-family: 'Noto Serif KR';"><b>메서드 내부</b></span></td></tr><tr style="height: 42px;"><td style="width: 25%; height: 42px;"><span style="font-family: 'Noto Serif KR';"><b>예시</b></span></td><td style="width: 25%; height: 42px;"><span style="font-family: 'Noto Serif KR';"><b>throw new MyException();</b></span></td><td style="width: 25%; height: 42px;"><span style="font-family: 'Noto Serif KR';"><b>void fn() throws MyException</b></span></td><td style="width: 25%; height: 42px;"><span style="font-family: 'Noto Serif KR';"><b>try { ... } catch (e) { ... }</b></span></td></tr></tbody></table>

#### **예시코드**

**try~catch : 예외 직접 처리**

```java
public void readFile() {
    try {
        FileReader fr = new FileReader("abc.txt");
    } catch (IOException e) {
        System.out.println("파일이 없습니다");
    }
}
```

**throws : 책임 위임**

```java
public void readFile() throws IOException {
    FileReader fr = new FileReader("abc.txt");
}
```

**throw : 예외 직접 발생**

```java
public void validateAge(int age) {
    if (age < 0) {
        throw new IllegalArgumentException("나이는 음수일 수 없습니다");
    }
}
```

#### **사용자 정의 예외 만들기**

**Unchecked**

```java
public class UserAccountNotApproved extends RuntimeException {
    public UserAccountNotApproved(String msg) { super(msg); }
}
```

-   **RuntimeException을 상속하면 throws 선언 필요 없음**
-   **인증, 권한, 유효성 검증 실패 등 → 직접 throw new...로 던지고**
-   **@ExceptionHandler로 글로벌하게 처리 가능**

#### **실무에서 언제 뭐 쓰나?**

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft"><tbody><tr><td style="width: 30.31%;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>상황</b></span></td><td style="width: 37.5194%;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>처리 방식</b></span></td><td style="width: 32.1705%;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>예시</b></span></td></tr><tr><td style="width: 30.31%;"><span style="font-family: 'Noto Serif KR';"><b>외부 자원 오류 (파일, DB, API)</b></span></td><td style="width: 37.5194%;"><span style="font-family: 'Noto Serif KR';"><b>Checked → throws 선언 또는 try-catch</b></span></td><td style="width: 32.1705%;"><span style="font-family: 'Noto Serif KR';"><b>IOException, SQLException</b></span></td></tr><tr><td style="width: 30.31%;"><span style="font-family: 'Noto Serif KR';"><b>사용자가 잘못된 입력을 줌</b></span></td><td style="width: 37.5194%;"><span style="font-family: 'Noto Serif KR';"><b>Unchecked → throw 직접</b></span></td><td style="width: 32.1705%;"><span style="font-family: 'Noto Serif KR';"><b>IllegalArgumentException</b></span></td></tr><tr><td style="width: 30.31%;"><span style="font-family: 'Noto Serif KR';"><b>로직 중 조건 위반 시</b></span></td><td style="width: 37.5194%;"><span style="font-family: 'Noto Serif KR';"><b>Unchecked 커스텀 예외 throw</b></span></td><td style="width: 32.1705%;"><span style="font-family: 'Noto Serif KR';"><b>UserAccountNotApproved</b></span></td></tr><tr><td style="width: 30.31%;"><span style="font-family: 'Noto Serif KR';"><b>복구 불가능한 오류</b></span></td><td style="width: 37.5194%;"><span style="font-family: 'Noto Serif KR';"><b>throw + 전파</b></span></td><td style="width: 32.1705%;"><span style="font-family: 'Noto Serif KR';"><b>예외를 로깅만 하고 다시 던짐</b></span></td></tr></tbody></table>

#### **Spring 실무 적용**

-   **예외 던짐: throw new UserNotFoundException(...)**
-   **예외 위임: throws IOException**
-   **예외 전역 처리**

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<?> handleUserNotFound(UserNotFoundException e) {
        return ResponseEntity.status(404).body(e.getMessage());
    }
}
```

#### **자주 하는 질문 정리**

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft"><tbody><tr><td style="width: 50%;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>질문</b></span></td><td style="width: 50%;"><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR';"><b>답변</b></span></td></tr><tr><td style="width: 50%;"><span style="font-family: 'Noto Serif KR';"><b>throw 하면 throws도 꼭 써야 하나요?</b></span></td><td style="width: 50%;"><span style="font-family: 'Noto Serif KR';"><b>RuntimeException 계열은 throws 없어도 됨</b></span></td></tr><tr><td style="width: 50%;"><span style="font-family: 'Noto Serif KR';"><b>커스텀 익센션(Exception)은 throws 안 써도 되나요?</b></span></td><td style="width: 50%;"><span style="font-family: 'Noto Serif KR';"><b>✅ RuntimeException 상속이면 생략 가능</b></span></td></tr></tbody></table>
