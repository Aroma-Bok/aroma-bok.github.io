---
title: "[Java] Gabage Collection (GC) 정리"
date: 2023-02-24 09:23:31 +0900
categories: ["Language", "Java"]
tags: ["gabage collection", "gc", "major gc", "minor gc", "가비지컬렉터"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-Gabage-Collection-GC-%EC%A0%95%EB%A6%AC"
---
**면접에서 GC 내용을 물어봐서 대답을 제대로 하지 못해 개념을 정리합니다.**

**단순하게 메모리영역 중 Heap영역 안에서 동적으로 할당된 객체에 대해서 다 사용한 객체를 알아서 삭제해 준다라고만**

**얘기했는데.. 뭔가 찜찜해서... 다시 정리합니다.**

* * *

**가비지 컬렉션 (Gabage Collection) 이란?**

**: GC라고도 부르며, 자바의 메모리 관리 방법 중 하나로 Java의 Heap 영역에서 동적으로 할당했던 메모리 영역 중 필요 없게 된 메모리 영역을 주기적으로 삭제하는 프로세스를 말합니다. C, C++에서는 이러한 GC가 없어 개발자가 직접 수동으로 메모리 할당과 해제를 일일이 해줘야 하는 반면, Java는 JVM에 탑재되어 있는 가비지 컬렉터가 메모리 관리를 대행해 주기 때문에 개발자는 개발에만 집중할 수 있는 장점이 있습니다.**

**가비지 컬렉션 (Gabage Collection) 장점**

1.  **개발자의 실수로 인한 메모리 누수를 막을 수 있다.**
2.  **개발자가 수동으로 메모리 해제를 해주지 않기 때문에 개발자는 개발에만 집중할 수 있다.**

**가비지 컬렉션 (Gabage Collection) 단점**

1.  **개발자가 메모리가 언제 해제되는지 정확하게 알 수 없다.**
2.  **GC가 동작하는 동안에는 다른 동작을 멈추기 때문에 오버헤드가 발생한다.**

더보기

**※ 오버헤드?** 

**: 어떠한 메모리 영역이 해제의 대상이 될지 검사하고 해제하는 일**

**이를 전문적인 용어로, Stop - The - World라고 한다.**

**Stop the World (STW)**

**: 가비지 컬렉션을 실행하기 위해 JVM이 애플리케이션의 실행을 멈추는 작업입니다. GC가 실행될 때는 GC를 실행하는 스레드를 제외한 모든 스레드들의 작업이 중단되고, GC가 완료되면 작업이 재개됩니다. 즉, GC가 돌아가는 동안에는 애플리케이션이 멈춘다는 말입니다. 그래서 보통 GC의 성능 개선을 위해 튜닝한다고 하면, 보통 STW의 시간을 줄이는 작업을 하는 것입니다.**

**이로 인해 GC가 너무 자주 실행되면 소프트웨어 성능 하락의 문제가 되기도 합니다.**

**가비지 컬렉션의 대상이 되는 객체**

![](/assets/img/posts/183/1.png)

*https://coding-factory.tistory.com/829*

**객체들은 Heap영역에서 생성되고 Method영역이나 Stack영역에서는 Heap영역에서 생성된 객체의 주소만 참조하는 형식으로 구성됩니다.**

**하지만 이렇게 생성된 Heap영역의 객체들이 메서드가 끝나는 등의 특정 이벤트들로 인하여 Heap영역의 객체 메모리 주소를 가지고 있는 참조 변수가 삭제되는 현상이 발생하게 됩니다. 이러한 객체들을 Unreachable 하다고 하며 주기적으로 가비지 컬렉터가 제거해 줍니다.**

더보기

**Reachable : 객체가 참조되고 있는 상태**

**Unreachable : 객체가 참조되고 있지 않은 상태 (GC의 대상)**

* * *

#### **GC 알고리즘 및 동작방식**

**Mark and Sweep**

**: 사용되는 객체를 확인하는 내부 알고리즘**

![](/assets/img/posts/183/2.png)

*https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EA%B0%80%EB%B9%84%EC%A7%80-%EC%BB%AC%EB%A0%89%EC%85%98GC-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC*

**가비지 컬렉션(GC)이 될 대상 객체를 식별(Mark)하고 제거(Sweep)하며 객체가 제거되어 파편화된 메모리 영역을 앞에서부터 채워나가는 작업(Compaction)을 수행하게 된다.**

**Mark : Root Space로부터 그래프 순회를 통해 연결된 객체들을 찾아내어 각각 어떤 객체를 참조하고 있는지 찾아서 마킹.**

**Sweep : 참조하고 있지 않은 객체 즉, Unreachable 객체들을 Heap에서 제거.**

**Compact : Sweep 후에 분산된 객체들을 Heap의 시작 주소로 모아 메모리가 할당된 부분과 그렇지 않은 부분으로 압축.**

더보기

**GC의 Root Space** 

**Mark And Sweep 방식은 루트로부터 해당 객체에 접근이 가능한지가 해제의 기준이 됩니다.**

**JVM GC에서의 Root Space는 Heap영역을 참조하는 Method영역, Stack영역이 됩니다.**

![](/assets/img/posts/183/3.png)![](/assets/img/posts/183/4.png)![](/assets/img/posts/183/5.png)

*https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EA%B0%80%EB%B9%84%EC%A7%80-%EC%BB%AC%EB%A0%89%EC%85%98GC-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC*

* * *

**여기서 좀 더 알아둬야 하는 내용은 Heap영역입니다.**

**GC는 Heap영역에서 일어나는 것이기 때문에 Heap영역을 알아두어야 한다고 생각합니다.**

**Java의 Heap영역은 Java8 기준으로 Young Generation, Old Generation으로 나눌 수 있습니다.**

**8 이전에는 Permanent 영역이 존재하였는데, Class의 Meta정보, Method의 Meta정보 등 상수 정보들이 저장되는 공간으로 활용되었지만, Java8 버전부터는 Permanent 영역이 Native영역으로 이동하며 Metaspace 영역으로 변경되었습니다.**

![](/assets/img/posts/183/6.png)

**Young Generation영역**

1.  **새롭게 생성된 객체가 할당되는 영역.**
2.  **대부분의 객체가 금방 Unreachable 상태가 되기 때문에, 많은 객체가 Young 영역에 생성되었다가 사라집니다.**
3.  **Young 영역에 대한 가비지 컬렉션을 Minor GC라고 부릅니다.**

**Old Generation영역**

1.  **Young 영역에서 Reachable 상태를 유지하여 살아남은 객체가 복사되는 영역.**
2.  **Young 영역보다 크게 할당되며, 영역의 크기가 큰 만큼 가비지는 적게 발생합니다.**
3.  **Old 영역에 대한 가비지 컬렉션을 Major GC or Full GC라고 부릅니다.**

![](/assets/img/posts/183/7.png)

**Old 영역이 더 큰 이유는, Young 영역의 수명이 짧은 객체들은 큰 공간을 필요로 하지 않으며 큰 객체들은 Young 영역이**

**아니라 바로 Old 영역에 할당되기 때문입니다.**

**그리고 Young 영역을 세분화할 수 있는데, Eden, Survival0, Survival1 3가지 영역으로 나눌 수 있습니다.**

![](/assets/img/posts/183/8.png)

**Eden**

1.  **new를 통해 새로 생성된 객체가 위치**
2.  **정기적인 쓰레기 수집 후 살아남은 객체들은 Survivor 영역으로 보냄**

**Survivor 0 / Survivor 1**

1.  **최소 1번의 GC이상 살아남은 객체가 존재하는 영역**
2.  **Survivor 영역에는 특별한 규칙이 있는데, Survivor 0 또는 Survivor 1 둘 중 하나에는 꼭 비어 있어야 하는 것.**

#### **Minor GC 과정**

**: Young 영역은 짧게 살아남는 메모리들이 존재하는 공간입니다. 모든 객체는 처음에는 Young 영역에 생성되게 됩니다. Young 영역은 Old에 비해 상대적으로 작기 때문에 메모리 상의 객체를 찾아 제거하는데 적은 시간이 걸립니다.**

**그래서 Young 영역에서 발생되는 GC를 Minor GC라 부릅니다.**

![](/assets/img/posts/183/9.png)

![](/assets/img/posts/183/10.png)

![](/assets/img/posts/183/11.png)

![](/assets/img/posts/183/12.png)

![](/assets/img/posts/183/13.png)

#### **Major GC (Full GC) 과정**

**: Old 영은 길게 살아남는 메모리들이 존재하는 공간입니다. Old 영역의 객체들은 처음에는 Young 영역에서 시작되었으나, GC과정 중에 제거되지 않은 경우 age 값이 차게 되어 이동된 객체들입니다.**

**이렇게 age값이 계속 차서 Old영역으로 넘어오게 되면서 메모리가 부족해지면 Major GC가 발생하게 됩니다.**

![](/assets/img/posts/183/14.png)

![](/assets/img/posts/183/15.png)

* * *

**Old 영역은 Young 영역에 비해 상대적으로 큰 공간을 가지고 있어, 이 공간에서 메모리 사이의 객체 제거에 많은 시간이**

**걸리게 됩니다. Minor GC는 애플리케이션에 크게 영향을 주지 않지만, Major GC는 보다 오래 시간이 걸리게 됩니다.**

**여기서 발생하는 게 위에서 언급한 STW (Stop - The - World) 문제가 발생하게 됩니다.**

![](/assets/img/posts/183/16.png)
