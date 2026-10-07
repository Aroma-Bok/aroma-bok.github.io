---
title: "[백준] 백준 3003 Java |  BufferedReader, readLine 메소드 feat NumberFormat"
date: 2022-11-21 18:46:24 +0900
categories: ["ETC", "알고리즘"]
tags: ["bufferedreader", "numberformat", "readline", "백준 3003 java"]
tistory_url: "https://aroma-bok.tistory.com/entry/%EB%B0%B1%EC%A4%80-%EB%B0%B1%EC%A4%80-3003-Java-BufferedReader-readLine-%EB%A9%94%EC%86%8C%EB%93%9C-feat-NumberFormat"
---
**Java에서 입력받을 때, Scanner 클래스를 이용해서 입력을 받는 연습을 했었다.**

**하지만, 알고리즘 문제를 연습하면 BufferedReader를 더 많이 사용하게 되었다.**

**알고리즘 문제라 시간이라는 리소스도 포함되고 있기에 조금이나마 차이를 두려고 많이 쓰는 것 같다.**

* * *

**먼저 BufferedReader와 BufferedWriter에 대해서 간단하게 알고 싶으면 아래 링크를 통해서 조금은 인지하자.**

**[2021.10.27 - \[Language/Java\] - \[JAVA\] BuffredReader & BufferedWriter](https://aroma-bok.tistory.com/entry/JAVA-BuffredReader-BufferedWriter)**

[\[JAVA\] BuffredReader & BufferedWriter](https://aroma-bok.tistory.com/entry/JAVA-BuffredReader-BufferedWriter)

```java
public class Chess {

	public static void main (String[] args) throws IOException{
		
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		
		int king = 1;
		int queen = 1;
		int rok = 2;
		int beshop =2;
		int night = 2;
		int pone = 8;
		
		StringTokenizer st = new StringTokenizer(br.readLine(), " ");
		
		king = king - Integer.parseInt(st.nextToken());
		queen= queen - Integer.parseInt(st.nextToken());
		rok = rok - Integer.parseInt(st.nextToken());
		beshop= beshop - Integer.parseInt(st.nextToken());
		night= night - Integer.parseInt(st.nextToken());
		pone= pone - Integer.parseInt(st.nextToken());

		System.out.print(king + " ");
		System.out.print(queen + " ");
		System.out.print(rok + " ");
		System.out.print(beshop + " ");
		System.out.print(night + " ");
		System.out.print(pone + " ");
		
		
	}
}
```

**위 코드의 결과는 아래처럼 잘 나온다.**

![](/assets/img/posts/180/1.png)

*한 줄로 받아옴*

**하지만 처음에 필자가 실수했던 코드는 아래와 같다.**

```java
public class Chess {

	public static void main (String[] args) throws IOException{
		
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		
		int king = 1;
		int queen = 1;
		int rok = 2;
		int beshop =2;
		int night = 2;
		int pone = 8;
		
		king = king - Integer.parseInt(br.readLine());
		queen= queen - Integer.parseInt(br.readLine());
		rok = rok - Integer.parseInt(br.readLine());
		beshop= beshop - Integer.parseInt(br.readLine());
		night= night - Integer.parseInt(br.readLine());
		pone= pone - Integer.parseInt(br.readLine());
		
		System.out.print(king + " ");
		System.out.print(queen + " ");
		System.out.print(rok + " ");
		System.out.print(beshop + " ");
		System.out.print(night + " ");
		System.out.print(pone + " ");

	}
}
```

**문제는 'Integer.parseInt(br.readLine());' 이 부분이다. 이 코드가 정상적으로 실행되려면**

**아래처럼 값이 입력되어야 한다.**

![](/assets/img/posts/180/2.png)

*하나씩 받아옴*

**하지만 문제에서는 하나의 열로 (한 줄)로 받아오기 때문에 이렇게 제출하면 NumberFormat 오류가 발생한다.**

![](/assets/img/posts/180/3.png)

*한 줄로 값을 넣으면 Exception 발생*

**즉, 주어진 입력값을 잘 보고 거기에 맞게 사용하여한다.**
