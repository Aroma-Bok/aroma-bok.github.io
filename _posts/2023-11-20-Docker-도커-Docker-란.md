---
title: "[Docker] 도커 (Docker) 란??"
date: 2023-11-20 15:43:58 +0900
categories: ["Solution & Tools", "Cloud & Container"]
tags: ["docker 개념", "도커", "도커 이미지", "도커 이미지 컨테이너 차이", "도커 컨테이너"]
tistory_url: "https://aroma-bok.tistory.com/entry/Docker-%EB%8F%84%EC%BB%A4-Docker-%EB%9E%80"
---
### **도커 (Docker)**

-   **Go언어로 작성된 리눅스 컨테이너 기반의 가상화 플랫폼**
-   **독립된 환경을 만들어서 하드웨어를 효율적으로 활용하는 기술**
-   **즉, 특정 서비스를 패키징하고 배포하는데 유용한 오픈소스 프로그램**

![](/assets/img/posts/252/1.png)

* * *

**※ 하이퍼바이저 및 가상화 그리고 컨테이너에 대한 개념을 알고 싶다면 작성해 둔 [내용](https://aroma-bok.tistory.com/entry/%EA%B0%80%EC%83%81%ED%99%94-%ED%95%98%EC%9D%B4%ED%8D%BC%EB%B0%94%EC%9D%B4%EC%A0%80-vs-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EC%B0%A8%EC%9D%B4%EC%A0%90-%EB%B0%8F-%EA%B0%9C%EB%85%90 "내용")을 참고하자**

* * *

#### **도커를 사용하는 이유?**

**애플리케이션 독립성을 가진다. 다른 컨테이너와도 독립된 공간을 보장받아 충돌이 발생하지 않는다.  
****컨테이너 내부에 작업 후 배포하려 한다면 도커 이미지로 만들어서 운영 서버에 전달만 하면 된다.  
****마이크로 서비스 구조로 변화가 쉽다. 컨테이너 하나당 하나의 기능을 제공하는 모듈로 만드는 등 조정이 가능하다.  
****즉, 환경에 구애받지 않고 애플리케이션을 신속하게 배포, 확장 가능하다.  
**

* * *

![](/assets/img/posts/252/2.png)

#### **도커 이미지 (Docker Image)**

-   **Docker Image란 컨테이너를 실행할 수 있는 실행파일 (읽기 전용)**
-   **컨테이너가 실행되기 위한 종속성(필요 파일, 라이브러리 등) 모두 가지고 있다.**
-   **즉, Docker Image만 있다면 어떤 환경에서든 같은 컨테이너를 돌릴 수 있다.**

#### **도커 컨테이너 (Docker Container)**

-   **도커 이미지를 실행한 상태(프로세스)**
-   **이미지로 컨테이너를 생성하면 이미지의 목적에 맞는 파일이 들어있는 파일 시스템과 격리된 시스템 자원 및** 
-   **네트워크를 사용할 수 있는 독립된 공간**

> **익숙한 VMware나 VirtualBox 같은 가상머신은 호스트 OS 위에 Guest OS 전체를 가상화하여 사용하는 방식이다.**  
> **이 방식은 여러 가지 OS를 가상화할 수 있고, 비교적 사용법이 간단하지만 무겁고 느려서 운영환경에선 사용할 수  
> 없었다.**  
>   
> **이를 개선하기 위해 프로세스를 격리하는 방식이 등장한다. 단순히 프로세스를 격리시키기 때문에 가볍고 빠르게  
> 동작한다. CPU나 메모리는 딱 프로세스가 필요한 만큼만 추가로 사용하고 성능적으로도 거의 손실이 없다.**

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft"><tbody><tr style="height: 19px;"><td style="width: 50.0%; height: 19px; text-align: center;"><b><span style="color: #ee2323;">도커 이미지 (Docker Image)</span></b></td><td style="width: 50.0%; height: 19px; text-align: center;"><b><span style="color: #ee2323;">도커 컨테이너 (Docker Container)</span></b></td></tr><tr style="height: 17px;"><td style="width: 50.0%; height: 17px; text-align: center;"><b>컨테이너의 설계도</b></td><td style="width: 50.0%; height: 17px; text-align: center;"><b>도커 이미지 인스턴스</b></td></tr><tr style="height: 17px;"><td style="width: 50.0%; height: 17px; text-align: center;"><b>변형 불가</b></td><td style="width: 50.0%; height: 17px; text-align: center;"><b>쓰기 가능</b></td></tr><tr style="height: 17px;"><td style="width: 50.0%; height: 17px; text-align: center;"><b>컨테이너 없이 존재 가능</b></td><td style="width: 50.0%; height: 17px; text-align: center;"><b>컨테이너는 이미지를 실행시켜야 존재</b></td></tr><tr style="height: 17px;"><td style="width: 50.0%; height: 17px; text-align: center;"><b>컴퓨팅 자원을 필요로 하지 않음</b></td><td style="width: 50.0%; height: 17px; text-align: center;"><b>실행을 위해 컴퓨팅 자원 필요</b></td></tr><tr style="height: 17px;"><td style="width: 50.0%; height: 17px; text-align: center;"><b>공개/비공개 레지스트리 플랫폼으로 공유</b></td><td style="width: 50.0%; height: 17px; text-align: center;"><b>이미 실행중인 개체는 공유 필요 없음</b></td></tr><tr style="height: 17px;"><td style="width: 50.0%; height: 17px; text-align: center;"><b>단 한번만 생성</b></td><td style="width: 50.0%; height: 17px; text-align: center;"><b>같은 이미지로부터 다수의 컨테이너 생성</b></td></tr></tbody></table>
