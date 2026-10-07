---
title: "[IT 용어] 넥서스(Nexus) Repository 란??"
date: 2022-05-11 21:48:41 +0900
categories: ["ETC", "IT Knowledge"]
tags: ["nexus", "넥서스", "넥서스?", "넥서스란"]
tistory_url: "https://aroma-bok.tistory.com/entry/IT-%EC%9A%A9%EC%96%B4-%EB%84%A5%EC%84%9C%EC%8A%A4Nexus-Repository-%EB%9E%80"
---
### **넥서스(Nexus) Repository?**

**: Maven에서 사용할 수 있는 가장 널리 사용되는 무료 Repository이다. 사설 Repository로 사용할 수 있으며, 코드 공유 등에서 사용할 수 있다. Docker와 Helm도 지원한다.**

**\* Sonatype에서 만든 저장소 관리자 프로젝트**

#### **장점** 

**1\. 회사/단체의 화이트리스트(WhiteList)로 인해 외부 Repository에 접속하기 어려운 경우 Proxy 역할**

**2\. 비상시 or 외부 인터넷이 느리거나 Repository가 다운되는 등 여러 상황에서도 빠르게 받을 수 있음**

**3\. 한번 다운로드 받은 Dependency는 로컬에 저장되어서 협업 시 다른 PC에도 설치해야 함**

**4\. 전체적인 일관성 증가**

**5\. 개발팀에서 사용하는 공통 라이브러리들을 공유**

#### **설치**

-   [**https://help.sonatype.com/repomanager3**](https://help.sonatype.com/repomanager3)
