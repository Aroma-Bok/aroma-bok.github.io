---
title: "[Effective Java] 아이템7 - 다 쓴 객체 참조를 해제하라"
date: 2023-11-27 12:00:54 +0900
categories: ["Language", "Effective Java"]
tags: ["effective java item7", "effective java 아이템7", "이펙티브 자바 아이템7", "이펙티브자바 아이템7"]
tistory_url: "https://aroma-bok.tistory.com/entry/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C7-%EB%8B%A4-%EC%93%B4-%EA%B0%9D%EC%B2%B4-%EC%B0%B8%EC%A1%B0%EB%A5%BC-%ED%95%B4%EC%A0%9C%ED%95%98%EB%9D%BC"
---
#### **메모리 누수의 주범 1 - **메모리 직접 관리****

**자바에 GC (Garbage Collector)가 있기 때문에, GC가 다 쓴 객체를 알아서 회수해 간다고 해서**

**메모리 관리에 더 이상 신경 쓰지 않아도 된다는 것은 큰 오해다.**

**아래 Stack을 사용하는 프로그램을 오래 실행하다 보면 점차 GC 활동과 메모리 사용량이**

**늘어나 결국 성능이 저하되는 코드를 볼 수 있다.**

**그렇다면, 어디서 메모리 누수가 나는지 확인해 보자.**

```java
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }
    public Object pop() {
        if (size == 0) {
            throw new EmptyStackException();
        }
        return elements[--size];
    }

    private void ensureCapacity() {
        if(elements.length == size) {
            elements = Arrays.copyOf(elements, 2 * size + 1);
        }
    }
}
```

**이 코드에서는 Stack이 커졌다가 줄어들었을 때, Stack에서 꺼내진 객체들을 GC가 회수하지 않는다.**

**이 Stack이 그 객체들의 다 쓴 참조를 여전히 가지고 있기 때문이다.**

**Stack에 계속 쌓다가 많이 빼내도 Stack이 차지하고 있는 메모리는 줄어들지 않는다.**

**가용한 범위(유의미한 값들을 갖고 있는 부분)는 elements 배열의 인덱스가 size보다 작은 부분이고,**

**그 값보다 큰 부분에 있는 값들은 필요 없이 메모리를 차지하고 있는 부분이다.**

**해당 참조를 다 사용했다면, null 처리 (참조 해제)를 해주자.**

**각 해당 원소의 참조가 더 이상 필요 없어지는 시점(Stack에서 꺼낼 때)에 null로 설정하여**

**다음 GC가 발생할 때, 레퍼런스(참조)가 정리되게 한다.**

**만약, null 처리한 참조를 실수로 사용하려 할 때, 프로그램이 NullPointerException을 던지며 종료할 수 있다.**

```java
    public Object pop() {
        if (size == 0) {
            throw new EmptyStackException();
        }
        Object result = elements[--size];
        elements[size] = null; // 다 쓴 참조는 null처리로 해제한다.
        return result;
    }
```

> **null 처리를 함으로써 실수로 잘못된 객체를 돌려주는 것보다는 NullPointerException을  
> 던지며 종료시키는 게** **더 나은 방법이라고 한다.**

* * *

#### **왜 메모리 누수에 취약했을까?**

**일반적으로 자기 메모리를 직접 관리하는 클래스라면 항시 메모리 누수에 주의해야 한다.**

**원소를 다 사용한 즉시 그 원소가 참조한 객체들을 다 null 처리를 해줘야 한다.**

**Stack이 자기 메모리를 직접 관리하기 때문이다.**

**이 Stack은 elements 배열로 저장소 풀을 만들어 원소들을 관리한다.**

**배열의 "활성 영역"에 속한 원소들이 사용되고 "비활성 영역"은 사용하지 않는다.**

**→ GC 입장에서는 활성영역, 비활성영역에 대해 알 수 없다. GC 입장에서는 참조하지 않는 객체만**

**처리하기 때문에, 비활성 영역에 있어도 참조하고 있다면, GC는 처리하지 않는다.**

**그래서 개발자는 비활성 영역이 되는 순간 null 처리해서 해당 객체를 더 이상 사용하지 않을 것임을** **GC에 알려줘야 한다.**

* * *

#### **객체 참조를 null 처리하는 일은 예외적이야 한다.**

**그렇지만 필요 없는 객체를 볼 때마다 null 처리하면, 오히려 프로그램을 필요 이상으로 지저분하게 만든다.**

**객체 참조를 null 처리하는 일은 예외적인 상황에서나 하는 것이지 일반적이지는 않다.**

**필요 없는 객체 레퍼런스를 정리하는 최선책은 그 레퍼런스를 가리키는 변수를 특정한 범위(Scope) 안에서만**

**사용하는 것이다. 변수의 범위를 가능한 최소가 되게 정의했다면 이 일은 자연스럽게 이루어진다.**

**아래가 그 예시 코드이다. Object size는 scope이 pop() 안에서만 형성되어 있으므로 scope 밖으로 나가면 무의미한**

**레퍼런스 변수가 되기 때문에 CG에 의해 정리가 된다. 굳이 null 처리를 하지 않아도 된다.**

```java
Object pop() {

	Object size = 24;

	...

	size = null; // X
```

* * *

#### **언제 레퍼런스를 null 처리해야 하는가?**

**메모리를 직접 관리하는 클래스는 메모리 누수를 조심해야 한다.**

**메모리를 직접 관리할 때, Stack 구현체처럼 elements라는 배열을 관리하는 경우에 GC는 어떤 객체가 필요 없는 객체인지 알 수 없으므로, 해당 레퍼런스를 null로 만들어 GC한테 필요 없는 객체들이라고 알려줘야 한다.**

* * *

#### **메모리 누수의 주범 2 -** **캐시, 메모리 누수의 주범**

**캐시를 사용할 때도 메모리 누수 문제를 조심해야 한다. 객체의 레퍼런스를 캐시에 넣어 놓고, 캐시를 비우는 것을 잊기 쉽다. 여러 가지 해결책이 있지만, 캐시의 키에 대한 레퍼런스가 캐시 밖에서 필요 없어지면 해당 엔트리를 캐시에서 자동으로**

**비워주는 WeakHashMap을 사용할 수 있다.**

#### **캐시 구현의 안 좋은 예시 - 객체를 사용한 뒤 key를 정리하지 않음**

```java
public static void main(String[] args) {
    Object key = new Object();
    Object value = new Object();

    Map<Object, Object> cache = new HashMap<>();
    cache.put(key, value);
    ...
}
```

**key의 사용이 없어지더라도 cache가 key의 레퍼런스를 가지고 있으므로, GC의 대상이 될 수 없다.**

#### **캐시 구현의 좋은 예시 - WeakHashMap 사용**

```java
    public static void main(String[] args) {
        Object key = new Object();
        Object value = new Object();

        Map<Object, Object> cache = new WeakHashMap<>();
        cache.put(key, value);
    }
```

**캐시 값이 무의미해진다면 자동으로 처리해 주는 WeakHashMap은 key 값을 모두 Weak 레퍼런스로 감싸  
Strong 레퍼런스가 없어지면 CG의 대상이 된다.**

**즉, WeakHashMap을 사용할 때, key 레퍼런스를 다 사용했다면 (key-value) 엔트리를 GC의 대상이 되도록 해 캐시에서  
자동으로 비워준다.**

> **객체를 만들듯이 만드는 모든 레퍼런스는 전부 Strong 레퍼런스이다.**  
> **Strong 레퍼런스를 Weak 레퍼런스로 감싸면 Weak 레퍼런스가 된다.**  
> **GC 대상이 되려면 해당 객체를 가리키는 레퍼런스가 전부 없어져야 한다.**  
>   
> **Weak 레퍼런스는 Strong 한 레퍼런스가 없어지면 Weak 레퍼런스도 GC의 대상이 될 수가 있다.**  
>   
> **※ Java의 참조유형에 대해서 알고 싶다면 [여기](https://aroma-bok.tistory.com/entry/Java-Java-%EC%B0%B8%EC%A1%B0-%EC%9C%A0%ED%98%95-%EC%A2%85%EB%A5%98)**

* * *

#### **메모리 누수의 주범 3 -** **콜백, 메모리 누수의 주범**

**세 번째로 흔하게 메모리 누수가 발생할 수 있는 지점으로 리스너와 콜백이 존재한다.**

**클라이언트 코드가 콜백을 등록할 수 있는 API를 만들고 콜백을 뺄 수 있는 방법을 제공하지 않는다면, 계속해서 콜백이 쌓이기만 할 것이다. 이것 역시 WeakHashMap을 사용해서 콜백을 Weak 레퍼런스로 저장하면 GC가 이를 즉시 수거해 해결할 수 있다.**
