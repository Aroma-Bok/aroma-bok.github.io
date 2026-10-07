---
title: "[JAVA] BuffredReader &  BufferedWriter"
date: 2021-10-27 23:12:51 +0900
categories: ["Language", "Java"]
tags: ["buffer", "bufferedreader", "bufferedwriter", "inputstream", "outputstream", "자바", "자바공부"]
tistory_url: "https://aroma-bok.tistory.com/entry/JAVA-BuffredReader-BufferedWriter"
---
**BufferedReader**라는 단어에서 볼 수 있듯이 '**Buffer : 완충제'** 역할을 하는 버퍼를 이용해서 읽는 함수 이다.

이 함수는 **버퍼를 이용하기 때문에 이 함수를 이용하면 입출력의 효율이 좋아진다고 한다.**

**(버퍼에 용량만큼 모았다가 전달)**

**\=> 대표적인 IO클래스 (Input/Output)**

**자바에서 데이터는 Stream을 통해 입출력됨**

**Stream은 단일 방향으로 연속적으로 흘러가는 것을 의미하고, 출발지에서 도착지로 흘러간다는 개념.**

**단방향 통신을 한다는 특징이 있기 때문에, 하나의 Stream으로 입출력을 동시에 할 수 없어서 같이 사용해야 함.**

**InputStream : 데이터 입력 받을 때 사용 (바이트기반 입력 스트림의 최상위 추상클래스)**

**OutputStream : 데이터를 출력할 때 사용 **(바이트기반 출력 스트림의 최상위 추상클래스)****

#### **Java.io 패키지의 주요 클래스**

![](/assets/img/posts/11/1.png)

*출처 : https://develop-im.tistory.com/54*

**\* 바이트 단위 : 그림, 멀티미디어, 문자 등 모든 종류의 데이터들을 주고 받을 수 있다.**

**\* 문자 단위 : Only 문자만 주고 받을 수 있다.**

* * *

하드디스크 or 외부 장치(ex:키보드)와의 데이터 입출력은 생각보다 시간이 걸리는 작업이라고 한다.

그래서 키보드가 눌릴 때마다 눌린 문자의 정보를 목적지로 바로 이동 시키는 것보다는 버퍼역할을 하는 버퍼를 두어서 데이터를 이동시키는 것(입력, 출력) 이 보다 **효율적**이고 **빠르다**고 한다.

![](/assets/img/posts/11/2.png)

*출처 : https://jhnyang.tistory.com/92*

그리고 **BufferedReader**는 **String만** 읽을 수 있기 때문에, **다른 데이터 타입은 형변환을 해주어야 한다**.

#### **사용법**

```java
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;

public class T2 {

	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader (new InputStreamReader(System.in));
        
       	         // 파일에서 입력을 받는 경우
          	  FileReader fr = new FileReader("파일명");
        	  BufferedReader br_f = new BufferedReader(fr);
        
		BufferedWriter bw = new BufferedWriter (new OutputStreamWriter(System.out));
		// 괄호안에 콘솔창으로 읽을건지, 파일로 읽을건지 등등에 따라 맞는 Reader & Writer를 넣는다.

		String str = br.readLine();
		//int i = Integer.parseInt(br.readLine()); => int로 형변환 할 경우
		// String이기 때문에 형변환을 안해도 된다.
        
		bw.write(str);
		//bw.write(String.valueOf(i));=> int로 형변환 할 경우
		// write만 하면 콘솔에 안나온다.

		bw.flush();
		// flush를 사용하면 한꺼번에 쏟아낸다. == 버퍼를 비우는 동작

		bw.close();
		br.close();
		// Scanner 닫듯이 이것도 닫아주자.
```

**★예외처리는 필수 ★ : throws IOExcetion 또는 try~catch 구문을 활용해야 한다.**

#### 주요메서드 - BufferedReader

<table style="border-collapse: collapse; width: 100%; height: 200px;" border="1" data-ke-align="alignLeft" data-ke-style="style13"><tbody><tr style="height: 20px;"><td style="width: 50%; height: 20px; text-align: center;">메서드</td><td style="width: 50%; height: 20px; text-align: center;">설명</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">void close()</td><td style="width: 50%; height: 20px;">입력 스트림을 닫고 사용하던 자원 해제</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">void mark(int, readAheadLimit)</td><td style="width: 50%; height: 20px;">스트림의 현재 위치를 마킹</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">boolean markSupported()</td><td style="width: 50%; height: 20px;">스트림이 mark 기능을 지원하는 boolean으로 알려줌</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">int read()</td><td style="width: 50%; height: 20px;">한 글자만 읽어 아스키코드 값으로 반환</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">int read(char[] cbuf, int offset, int length)</td><td style="width: 50%; height: 20px;">cbuf의 offset위치부터 length의 길이만큼 문자를 스트림으로<br>읽어온다</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">String readLine()</td><td style="width: 50%; height: 20px;">한 줄을 읽어 온다</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">boolean ready()</td><td style="width: 50%; height: 20px;">입력 스트림이 사용할 준비가 되어있는지 알려준다</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">void reset()</td><td style="width: 50%; height: 20px;">읽는 위치를 처음으로 변경.<br>IF) 마킹이 되어있다면, 마킹된 위치로 변경</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">long skip(long n)</td><td style="width: 50%; height: 20px;">n개의 문자를 건너 뛴다.</td></tr></tbody></table>

#### 주요메서드 - BufferedWriter

<table style="border-collapse: collapse; width: 100%; height: 120px;" border="1" data-ke-align="alignLeft" data-ke-style="style13"><tbody><tr style="height: 20px;"><td style="width: 50%; height: 20px; text-align: center;">메서드</td><td style="width: 50%; height: 20px; text-align: center;">설명</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">void close()</td><td style="width: 50%; height: 20px;">스트림을 닫는다.</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">void flush()</td><td style="width: 50%; height: 20px;">스트림을 비운다.</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">void newLine()</td><td style="width: 50%; height: 20px;">개행 문자를 출력.</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">void write(char[] cbuf, int offset, int length)</td><td style="width: 50%; height: 20px;">cbuf의 offset위치부터 length만큼 출력.</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">void write(int c)</td><td style="width: 50%; height: 20px;">한 글자를 출력</td></tr><tr><td style="width: 50%;">void write(String s, int offset, int length)</td><td style="width: 50%;">s문자열에서 offset부터 length만큼 출력.</td></tr></tbody></table>

#### **Scanner 클래스와의 차이점**

**Scanner 클래스 : Enter, Space 모두 경계로 입력값을 인식 (공백 단위로 끊음)**

**BufferedReader 클래스 : Enter만을 경계로 입력값을 인식**

**(데이터의 양이 적을 때는 차이가 미미하지만, 많은 양의 데이터를 처리하는 경우에는**

**버퍼에 모아서 한 번에 전달하는것이 빠르고 효율적이라고 한다.)**
