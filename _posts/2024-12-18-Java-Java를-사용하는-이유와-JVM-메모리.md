---
title: "[Java] Java를 사용하는 이유와 JVM 메모리"
date: 2024-12-18 13:40:30 +0900
categories: ["Language", "Java"]
tags: ["heap stack 영역", "java jvm", "jvm", "jvm 메모리 구조", "method heap stack", "runtime data area", "자바를 사용하는 이유"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-Java%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0%EC%99%80-JVM-%EB%A9%94%EB%AA%A8%EB%A6%AC"
---
### **Java를 사용하는 이유**

**1\. 플랫폼 독립성  
▶ Java 코드는 JVM(Java Virtual Machine)에서 실행되므로, 운영체제에 종속되지 않는다  
**

**2\. 객체지향**

**3\. 다양한 라이브러리와 프레임워크**

**4\. 성능**

**5\. 멀티스레드와 동시성**

**6\. 보안**

![](/assets/img/posts/284/1.png)

* * *

**Java를 사용하는 이유라면 많이 있겠지만, 여기서 '플랫폼 독립성'에 대해서 더 설명해보려고 한다.**

### **JVM(Java Virtual Machine)**

-   **Java 애플리케이션을 실행하는 가상 머신**
-   **Java 코드가 운영 체제의 제약을 받지 않고 실행될 수 있도록 한다**
-   **Java 프로그램을 바이트코드로 실행**

#### **JVM 역할**

-   **바이트코드 실행**  
    **▶ Java 소스 코드(. java)는 컴파일러를 통해 바이트코드(. class)로 변환하여 실행**
-   **플랫폼 독립성**  
    **▶ 운영 체제와 하드웨어에 관계없이 동일한 방식으로 바이트코드를 실행**
-   **JIT 컴파일**  
    **▶ JIT(Just-In-Time) 컴파일러를 사용하여 실행 시간에 바이트코드를 네이티브 머신코드로 변환하고 최적화**

![](/assets/img/posts/284/2.png)

* * *

### **JVM 구조**

-   **JVM은 실행 중인 Java 애플리케이션을 위해 여러 종류의 메모리 영역을 관리**
-   **JVM의 구조는 크게 GC(Gabage Collector), Execution Engine, Class Loader, Runtime Data Area 4가지 존재**

![](/assets/img/posts/284/3.png)

### **GC(Gabage Collector)**

-   **힙(Heap) 메모리 영역에 생성된 객체들 중 더 이상 참조되지 않는 객체를 자동으로 제거**

[2023.02.24 - \[Language/Java\] - \[Java\] Gabage Collection (GC) 정리](https://aroma-bok.tistory.com/entry/Java-Gabage-Collection-GC-%EC%A0%95%EB%A6%AC)

### **Execution Engine**

-   **메모리에 적재된 바이트코드(. class)를 기계어로 변환해 명령어 단위로 읽어 실행하는 역할**
-   **인터프리터와 JIT 컴파일러 존재**

> **인터프리터**  
> **→ 바이트코드(. class)를 한 줄씩 읽어서 실행**  
>   
> **JIT(Just-In-Time) 컴파일러**  
> **→ 프로그램 실행 중에 바이트코드 전체 또는 일부를 네이티브 코드로 컴파일하고, 직적 실행**  
> **→ 초기 컴파일에는 시간이 걸리지만, 한 번 컴파일된 코드는 빠르게 실행**  
> **→ 자주 실행되는 코드를 분석해 우선적으로 컴파일하여 성능을 최적화**  
>   
> **프로그램 실행 초기에는 인터프리터 방식으로 빠르게 시작하고, 실행 중에 JIT 컴파일러가  
> 분석을 통해 성능이** **중요한 부분을 식별해 네이티브 코드로 컴파일한다**

### **Class Loader**

-   **Java는 컴파일 타임이 아니라 런타임(. class 파일을 실행할 때) 시점, 즉, 클래스를 처음으로 참조할 때 해당 클래스를 로드하고 링크하며 초기화한다. (동적 로딩)**

### **Runtime Data Area**

-   **JVM이 운영체제 위에서 실행될 때, 할당받는 메모리 영역**
-   **Method | Heap | Stack | PC Register | Native Method Stack**
-   **Method | Heap 영역은 스레드에서 공유되고, 나머지 영역은 스레드마다 각각 존재**

![](/assets/img/posts/284/4.png)

* * *

### **Runtime Data Area**

-   **Method(Static)  
    → 모든 스레드가 공유하는 영역으로, 클래스 수준의 정보(클래스 이름, 부모 클래스 이름, 메서드와 변수 정보 등)  
    → 프로그램이 종료될 때까지 메모리에 존재하고 어디서든 사용 가능  
    **
-   **Heap  
    → 모든 스레드가 공유하는 영역으로, 생성된 모든 객체와 배열이 저장되는 영역  
    → Stack 영역과 다르게 보관되는 메모리가 호출이 끝나도 삭제되지 않고 유지  
    → 그러나 GC가 더 이상 참조하지 않는 인스턴스가 있다면 제거  
    → Heap 영역에 있는 객체들을 가리키는 참조를 위한 주소값은 Stack에 포함된다  
    **
-   **Stack  
    → 스레드마다 별도로 존재하며, 스레드의 메서드 호출과 로컬 변수 등을 관리 (메서드 단위)  
    → 메서드가 종료되면 메모리에서 사라짐  
    **
-   **PC Register  
    → 스레드마다 별도로 존재하며, 스레드가 시작될 때 생성  
    → 현재 실행 중인 JVM 명령의 주소값을 가진다  
    **
-   **Native Method Stack  
    → Java가 아닌 언어로 작성된 네이티브 코드를 위한 메모리 영**
