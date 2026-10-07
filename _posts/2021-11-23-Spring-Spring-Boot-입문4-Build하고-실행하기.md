---
title: "[Spring] Spring Boot 입문(4) - Build하고 실행하기"
date: 2021-11-23 23:24:45 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["build", "스프링", "스프링 부트 입문", "스프링부트", "스프링입문"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-Spring-Boot-%EC%9E%85%EB%AC%B84-Build%ED%95%98%EA%B3%A0-%EC%8B%A4%ED%96%89%ED%95%98%EA%B8%B0"
---
CMD를 이용해서 Build를 진행해보자. (※이클립스에서 먼저 서버를 꺼준다.)

![](/assets/img/posts/20/1.png)

라이브러리를 자동으로 다운받거나 build가 자동으로 된다.

완료되면 build-> libs폴더 안에 파일이 만들어져 있다.

![](/assets/img/posts/20/2.png)

CMD에서 새로 생긴 파일을 실행시키면 서버가 실행이되면서 local페이지를 들어갈 수 있다.

  
서버 배포할때는 이 파일(jar)만 복사해서 서버에 넣어주면 된다고 한다.

![](/assets/img/posts/20/3.png)

![](/assets/img/posts/20/4.png)

※ 잘 안된다면, ./gradlew clean build 를 실행하자. 지우고 다시 build를 해준다고 한다.

출처 - 인프런(스프링 입문 - 스프링부트)
