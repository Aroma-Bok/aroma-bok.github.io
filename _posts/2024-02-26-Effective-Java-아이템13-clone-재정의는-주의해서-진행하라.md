---
title: "[Effective Java] 아이템13 - clone 재정의는 주의해서 진행하라"
date: 2024-02-26 15:32:54 +0900
categories: ["Language", "Effective Java"]
tags: ["effective java item 13", "effective java item13 clone", "이펙티브 자바 아이템 13"]
tistory_url: "https://aroma-bok.tistory.com/entry/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C13-clone-%EC%9E%AC%EC%A0%95%EC%9D%98%EB%8A%94-%EC%A3%BC%EC%9D%98%ED%95%B4%EC%84%9C-%EC%A7%84%ED%96%89%ED%95%98%EB%9D%BC"
---
**clone() 메서드는 객체의 모든 필드를 복사하여 새로운 객체에 넣어 반환하는 동작을 수행한다.**  
**즉, 필드의 값이 같은 객체를 새로 만드는 것이다.**

* * *

#### **Clonable의 역할**

**\- 복제해도 되는 클래스임을 나타내는 믹스인 인터페이스**  
**\- Object 클래스에 protected clone()이라는 메서드가 있다.**  
**\- Clonable 인터페이스는 clone() 메서드의 동작방식을 결정한다.**  
**\- Clonable을 구현하지 않은 인스턴스에서 clone()을 호출하면 CloneNotSupportedException을 던진다.**

> **믹스인( Mixed in ) 인터페이스  
> **  
> **객체지향언어에서 다른 클래스에서 '사용'할 목적으로 만들어진 클래스 \- '포함(has-a)'으로 설명된다.  
> '상속(is-a)'과 비교되는 개념. Composition 혹은 Aggregation으로 불린다.  
> 코드 재사용성을 높여주고, 상속의 단점을 해결  
> 자바 코드에서는 다중 상속의 제한이 없는 인터페이스로 구현하기 용이  
> 대상 타입의 주된 기능에 선택정 기능을 '혼합(Mixed in)'한다고 해서 믹스인이라고 불린다.**

* * *

#### **clone() 메서드 일반 규약**

1.  **x.clone()!= x 식은 true**  
    **→ 복사된 객체가 원본이랑 같은 주소를 가지면 안 된다.**
2.  **x.clone(). getClass() == x.clone(). getClass() 식은 true  
    →  복사된 객체가 같은 클래스여야 한다는 뜻이다.  
    **
3.   **x.clone(). equals()는 true이다. 하지만, 필수는 아니다.  
    →  복사된 객체가 논리적 동치는 일치해야 한다는 뜻이다. (필수는 아니다.)**

* * *

#### **clone() 메서드는 super.clone()을 사용하는 편이 좋다.**

-    **super.clone()을 사용하지 않으면, 상속한 하위 클래스에서 clone()을 호출했을 때, 엉뚱한 결과가 나올 수 있다.**
-   **단, final 클래스라면, 이런 걱정을 할 필요 없다.**

* * *

#### **clone() 호출 시, 필요사항**

-   **Object.clone()은 기본적으로 protected이기 때문에 하위 클래스에서 public으로 오버라이드 해주어야 한다.**
-   **반환 타입은 Object가 아닌 해당 클래스 타입으로 하는 것이 좋다.  
     (클라이언트가 형 변환 할 필요가 없음. 재정의한 메서드의 반환 타입은 상위 클래스의 메서드가 반환하는  
     타입의 하위 타입일 수 있다.)**
-   **Clonable을 구현해야 한다. Object.clone()에서는 실제 객체의 크기를 알아내고 복사하는데,  
    이때 실제 객체가 Clonable을 구현했는지 확인하고 구현하지 않았다면 CloneNotSupportedException을 던진다.**
-   **오버라이드 한 메서드의 내부 구현은 super.clone() 호출부터 시작되어야 한다.**
-   **모든 필드가 기본 타입이거나 불변 객체를 참조한다면 더 이상 필요한 작업은 없다.**
-   **생성자 연쇄와 비슷하지만 강제성이 없다는 점에서 차이가 있다.**

* * *

#### **clone() 호출 시, 주의사항**

-   **상위 클래스의 clone() 메서드에서 super.clone()을 호출하지 않는 경우**  
    **→  이 경우 하위 클래스에서 clone() 메서드를 호출하더라도 제대로 동작하지 않게 된다.**
-    **최종적으로 실행되는 Object.clone() 메서드는 얕은 복사를 제공하기 때문에 참조 타입을 가지는 경우에는  
     반환하기 전에 필드를 수정해야 한다.**
-   **Object.clone()은 동기화를 신경 쓰지 않은 메서드이다. 즉, 동시성 문제가 발생할 수 있다.**
-   **기본 타입이나 불변 객체 참조만 가지면 아무것도 수정할 필요가 없으나, 일련번호 혹은 고유 ID와 같은 값을 가지고 있다면, 비록 불변일지라도 새롭게 수정해주어야 한다.**

* * *

#### **Cloneable을 implements을 하지 않았을 때**

```java
Exception in thread "main" java.lang.CloneNotSupportedException: Item13.Person
```

#### **Cloneable을 implements 하고 결과 확인 했을 때**

```java
원본 객체 : Item13.Person@2f4d3709
원본 객체 : 이름: 테스트 / 나이:1
클론 객체 : Item13.Person@133314b
클론 객체 : 이름: 테스트 / 나이:1
```

* * *

#### **가변객체를 참조할 때의 clone() 메서드**

> **가변객체 : instance 생성 이후에도 상태 변경이 가능한 객체를 말한다.**

-   **이 클래스를 그대로 복제하면, primitive 타입의 값은 올바르게 복제되지만,  
    복제된 Stack의 elements는 복제 전의 Stack과 같은 배열을 가리키게 된다.**
-   **두 스택에 같은 elements가 들어있고, 하나를 바꾸면 다른 하나도 연동된다는 뜻이다.  
    이건 우리가 원한 clone()이 아니다.**
-   **Stack 클래스의 유일한 생성자를 이용하면 이런 문제는 없을 것이다.  
    하지만 값이 복사되지 않는 문제가 있다.**

* * *

**Stack 클래스가 있고, 이 클래스를 복사한다고 가정하자.  
단순하게, super.clone()만 호출하면 복사 객체와 원본 객체의 elements는 동일한 메모리를 공유하기 때문에 하나를 수정하면 다른 하나도 수정되게 된다.**

```java
public class Stack implements Cloneable{
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        this.elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public Object pop() {
        if (size == 0) {
            throw new EmptyStackException();
        }
        Object result = elements[--size];
        elements[size] = null; // 다 쓴 참조 해제
        return result;
    }

    private void ensureCapacity() {
        if (elements.length == size) {
            elements = Arrays.copyOf(elements, 2 * size + 1);
        }
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
            Stack result = (Stack) super.clone();
            return result;
    }

    @Override
    public String toString() {
        return "Stack{" +
                "elements=" + Arrays.toString(elements) +
                ", size=" + size +
                '}';
    }
}
```

```java
stack1 = Stack{elements=[value1, value2, value3, null, null, null, null, null, null, null, null, null, null, null, null, null], size=2}
stack2 = Stack{elements=[value1, value2, value3, null, null, null, null, null, null, null, null, null, null, null, null, null], size=3}
```

**의도했던 거와는 달리, elements 배열을 참조하여 동일하게 수정이 된다.**

**아래처럼 clone() 메서드를 수정해서 다시 테스트를 해보자.**

```java
@Override
protected Object clone() throws CloneNotSupportedException {
    try {
        Stack result = (Stack) super.clone();
        result.elements = elements.clone();
        return result;
    } catch (CloneNotSupportedException e) {
        throw new RuntimeException(e);
    }
}
```

```java
stack1 = Stack{elements=[value1, value2, null, null, null, null, null, null, null, null, null, null, null, null, null, null], size=2}
stack2 = Stack{elements=[value1, value2, value3, null, null, null, null, null, null, null, null, null, null, null, null, null], size=3}
```

**처음 테스트와 달리, 의도했던 대로 복사가 잘 진행된 것을 확인할 수 있다.** 

**즉, 서로 같은 elements 배열을 참조하지 않는다.**  
**만약 elements가 final로 선언되어 있었다면, 앞의 방식은 작동하지 않는다.** 

* * *

#### **가변객체 내부에 가변객체가 있을 때의 clone() 메서드**

```java
package Item13;

public class HashTable {
    private Entry[] buckets;

    private static class Entry {
        final Object key;
        Object value;
        Entry next;

        public Entry(Object key, Object value, Entry next) {
            this.key = key;
            this.value = value;
            this.next = next;
        }
    }

    @Override
    public HashTable clone() throws CloneNotSupportedException {
        HashTable result = (HashTable) super.clone();
        result.buckets = buckets.clone();
        return result;
    }
    // clone() 메서드를 사용하면, 복제된 HashTable은 자신만의 buckets을 가지겠지만, buckets 내부에 있는 객체들은
    // 여전히 복제되기 이전의 객체들을 가리키고 있을것이다.
}
```

**clone() 메서드를 사용하면, 복제된 HashTable은 자신만의 buckets을 가지겠지만,**

**buckets 내부에 있는 객체들은 여전히 복제되기 이전의 객체들을 가리키고 있을 것이다.**

```java
public class HashTable {
    private Entry[] buckets;

    private static class Entry {
        final Object key;
        Object value;
        Entry next;

        public Entry(Object key, Object value, Entry next) {
            this.key = key;
            this.value = value;
            this.next = next;
        }
        Entry deepCopy() {
            Entry result = new Entry(key, value, next);
            for(Entry p = result; p.next != null; p = p.next) {
                p.next = new Entry(p.next.key, p.next.value, p.next.next);
            }
            return result;
        }
    }

    @Override
    public HashTable clone() throws CloneNotSupportedException {
        HashTable result = (HashTable) super.clone();
        result.buckets = new Entry[buckets.length];

        for (int i=0; i<buckets.length; i++) {
            if(buckets[i] != null) {
                result.buckets[i] = buckets[i].deepCopy();
            }
        }
        return result;
    }
}
```

**HashTable의 경우, 같은 HashCode를 가지고 실제 논리적 동치가 아닌 key 값이 들어왔을 때,** 

**LinkedList처럼 next로 연결된다.**

-   **이 경우, 계속 객체로 연결되어 있는데, 이 객체를 연쇄적으로 계속 같은 값을 지닌 새로운 객체로 복사해주어야 한다.**
-   **그 부분이 위쪽에서 deepCopy()를 반복하는 부분이다.**
-   **deepCopy() 메서드 내부에서는 연결된 모든 엔트리를 내용이 같은 새로운 객체로 복사하고 있다.**

* * *

#### **정리**

-   **인터페이스를 만들 때는 Cloneable을 확장해서는 안된다.**  
    **→ Cloneable은 클래스의 믹스인 의도로 만들어진 것이다.**
-   **final 클래스라면 Cloneable을 구현해도 위험은 크지 않지만, 성능 최적화 관점에서 검토 후에 드물게 허용한다.**
-   **복제 기능은 생성자와 팩토리를 이용하는 것이 좋다.**  
    **→ 단 한 가지 예외 상황은, 배열을 복사할 때이다.**
