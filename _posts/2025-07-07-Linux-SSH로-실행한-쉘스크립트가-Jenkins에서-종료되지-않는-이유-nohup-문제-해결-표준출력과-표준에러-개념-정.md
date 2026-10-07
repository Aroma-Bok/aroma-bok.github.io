---
title: "[Linux] SSH로 실행한 쉘스크립트가 Jenkins에서 종료되지 않는 이유 (nohup 문제 해결) + 표준출력과 표준에러 개념 정리"
date: 2025-07-07 14:32:23 +0900
categories: ["Solution & Tools", "Linux"]
tags: ["jenkins 파이프라인 실행 대기", "nohup", "ssh 세션", "stderr", "stdout", "리디렉션"]
tistory_url: "https://aroma-bok.tistory.com/entry/Linux-SSH%EB%A1%9C-%EC%8B%A4%ED%96%89%ED%95%9C-%EC%89%98%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EA%B0%80-Jenkins%EC%97%90%EC%84%9C-%EC%A2%85%EB%A3%8C%EB%90%98%EC%A7%80-%EC%95%8A%EB%8A%94-%EC%9D%B4%EC%9C%A0-nohup-%EB%AC%B8%EC%A0%9C-%ED%95%B4%EA%B2%B0-%ED%91%9C%EC%A4%80%EC%B6%9C%EB%A0%A5%EA%B3%BC-%ED%91%9C%EC%A4%80%EC%97%90%EB%9F%AC-%EA%B0%9C%EB%85%90-%EC%A0%95%EB%A6%AC"
---
#### **문제상황**

**Jenkins에서 아래와 같이 ssh를 통해 원격 서버에 배포/기동 스크립트를 실행했다.**

```java
def status = sh (
    script: '''
    ssh -p 9999 jboss@10.x.x.x '
        set -e
        cd /NAS/a && ./move.sh
        cd /NAS/b/back_up_shell && ./back_up.sh
    '
    ''',
    returnStatus: true
)
```

**쉘 스크립트 내부에서는 nohup을 이용하여 jboss를 실행한다.**

```java
nohup $JBOSS_HOME/bin/standalone.sh ... >> $LOG_HOME/nohup/...out &
```

**이 스크립트는 로컬에서 직접 실행하면 정상 종료되는데, Jenkins에서는 계속 실행 중 상태로 남아있는다.**

* * *

#### **원인분석 : SSH 세션이 종료되지 않는 이유**

-   **쉘에서 실행되는 모든 명령은 기본적으로 결과 메시지를 터미널(세션)에 출력한다.**
-   **nohup은 stdout만 리다이렉션 하면 stderr는 여전히 세션에 남아 있기 때문 세션이 종료되지 않을 수 있다.**

* * *

**해결방법 : nohup 리다이렉션 수정**

-   **nohup 명령어를 사용할 때, stdout뿐 아니라 stderr도 함께 리다이렉션 하며 터미널 출력이 모두 로그파일로 빠지고,  
    ****ssh 세션이 바로 종료될 수 있었다.**

```java
#잘못된 사용
nohup $JBOSS_HOME/bin/standalone.sh ... >> $LOG_HOME/nohup/...out &

#올바른 사용
nohup $JBOSS_HOME/bin/standalone.sh ... >> $LOG_HOME/nohup/...out 2>&1 &
```

* * *

#### **표준 출력(stdout)과 표준 에러(stderr)의 개념**

**컴퓨터에서 프로그램이 실행될 때, 결과나 메시지를 어디에 어떻게 보낼지 정해진 통로(스트림)가 있다.**  

**표준 출력 (stdout)**  

-   **프로그램이 일반적인 결과나 메시지를 보내는 통로**
-   **보통 터미널 화면에 보이는 텍스트**
-   **예: ls 명령어가 “파일 목록”을 출력하는 게 표준 출력.**

**표준 에러 (stderr)**  

-   **프로그램이 오류 메시지를 보내는 통로**
-   **프로그램 실행 중 문제가 생겼을 때 사용자에게 알려주는 메시지들이 여기에 포함**
-   **예: 파일이 없거나 권한이 없을 때 나오는 “파일을 찾을 수 없음” 같은 메시지.**  
      
    

**왜 분리했을까?**  

-   **정상 출력과 에러 메시지를 구분해서 처리를 하기 위해서**
-   **예를 들어, 파일 목록만 보고 싶을 때, 에러 메시지 때문에 내용이 섞이면 혼란스럽다.  
    ****그래서 프로그램이 출력하는 내용을 두 갈래로 나눠서 관리**

**터미널에서의 동작**  

-   **기본적으로 표준 출력과 표준 에러는 모두 터미널(화면)로 출력돼서 눈에 보이지만,  
    ****내부적으로는 별개의 스트림**

**리다이렉션 (출력 방향 바꾸기)**  

-   **보통 터미널에 출력되는 걸 파일로 저장하거나 다른 프로그램으로 보내고 싶을 때, 리다이렉션(출력 방향 전환)**

```java
> : 표준 출력을 파일로 보낸다.
예: ls > file_list.txt → ls 결과를 file_list.txt에 저장

2> : 표준 에러를 파일로 보낸다.
예: ls no_such_file 2> error_log.txt → 없는 파일을 조회할 때 에러 메시지를 error_log.txt에 저장

2>&1 : 표준 에러(2)를 표준 출력(1)과 같은 곳으로 보낸다.
예: some_command > all_output.txt 2>&1 → 정상 출력과 에러를 모두 all_output.txt에 저장
```

**왜 nohup에서 표준 출력과 에러를 다 처리해야 하나?**  

-   **nohup 명령어는 터미널과 분리해서 백그라운드 실행**
-   **표준 출력과 표준 에러를 처리하지 않으면 프로세스가 터미널에 출력하려고 하면서 중간에 멈출 수도 있다.**
-   **그래서 보통 nohup some\_command > out.log 2>&1 &처럼 표준 출력과 에러를 한꺼번에 로그 파일로  
    보****내서 문제를 막음.**
