---
title: "[Java] serialVersionUID"
date: 2023-09-12 20:32:49 +0900
categories: ["Language", "Java"]
tags: ["private static final long serialversionuid = 1l;", "serializable", "serialization deserialization", "serialversionuid", "직렬화 역직렬화"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-serialVersionUID"
---
**serialVersionUID는 Java 직렬화(Serialization)에서 사용되는 필드로, 직렬화된 객체의 버전을 식별하기**

**위한 역할을 한다.**

* * *

**먼저 직렬화(Serialization)와 역직렬화(Deserialization)는 데이터를 이동하거나 저장하는 과정에서 객체의 상태를**

**변환하고 복구하는 기술이다.** 

**이는 객체 지향 프로그래밍 언어에서 객체를 '바이트 스트림' 또는 다른 형태의 데이터로 변환하고**

**다시 객체로 변환하는 프로세스를 의미한다. Java에서는 직렬화와 역직렬화를 위해 내장 기능을 제공한다.**

* * *

#### **바이트 스트림 (Byte Stream)**

-   **데이터를 바이트(Byte)의 형태로 처리하는 입출력 스트림**
-   **바이트 단위로 읽고 쓰는 데 사용되며, 주로 텍스트 데이터, 이미지, 동영사 등의 이진 데이터를 처리할 때 활용**
-   **Java에서는 FileInputStream, FileOutputStream 클래스를 사용**
-   **네트워크 통신을 할 때, Socket 클래와 같은 클래스를 사용하여 바이트 스트림을 처리**

* * *

#### **직렬화(Serialization)**

-   **직렬화는 객체를 바이트 스트림 또는 일련의 바이트로 변환하는 프로세스**
-   **직렬화를 통해 객체의 상태와 데이터를 외부로 내보낼 수 있다**
-   **java.io.Serializable 인터페이스를 구현한 클래스를 직렬화할 수 있다.**

**직렬화 단계**

1.  **직렬화 가능한 객체 생성 : Serializable 인터페이스를 구현한 클래스의 인스턴스 생성**
2.  **객체를 출력 스트림에 쓰기 : ObjectOutputStream을 사용하여 객체를 직렬화하고 바이트 스트림에 기록**
3.  **직렬화된 데이터 전송 또는 저장 : 직렬화된 바이트 스트림은 파일에 저장하거나 네트워크를 통해 전송**

![](/assets/img/posts/217/1.png)

*직렬화*

* * *

#### **역직렬화(Deserialization)**

-   **역직렬화는 직렬화된 데이터를 다시 객체로 변환하는 프로세스**
-   **ObjectInputStream을 사용하여 역직렬화 수해**

**역직렬화 단계**

1.  **직렬화된 데이터 읽기 : ObjectInputStream을 사용하여 직렬화된 데이터를 읽는다.**
2.  **객체 복원 : 읽어 들인 직렬화된 데이터를 기반으로 객체를 복원**
3.  **역직렬화된 객체 사용 : 역직렬화된 객체를 프로그램에서 사용**

![](/assets/img/posts/217/2.png)

*역직렬화*

* * *

#### **serialVersionUID**

-   **Java 직렬화(Serialization)에서 사용되는 필드로서 직렬화된 객체의 버전을 식별하기 위한 역할**

**serialVersionUID 사용하는 이유**

1.  **버전 호환성 : 클래스를 직렬화한 후에 클래스의 구조가 변경되면 이전 직렬화된 데이터와의 호환성 문제가 발생할 수 있다. serialVersionUID를 명시적으로 설정하면 이전 버전과 새 버전 간의 직렬화 호환성을 제어할 수 있다.**
2.  **런타임 예외 방지 : serialVersionUID를 설정하지 않고 클래스 구조를 변경하면 직렬화된 데이터를 역직렬화하는 동안 InvalidClassException과 같은 런타임 예외가 발생할 수 있다. serialVersionUID를 설정함으로써 예측가능한  
    동작을 제공할 수 있다.**
3.  **보안 : 직렬화는 시스템 간 데이터 전송 및 저장에 사용된다. 클래스의 직렬화 버전을 명시적으로 설정하면  
    클래스 버전이 미래에 변경될 때 데이터 무결성과 보안을 강화할 수 있다.**

![](/assets/img/posts/217/3.png)

* * *

**따라서 serialVersionUID는 클래스의 직렬화에 대한 중요한 역할을 한다. 클래를 직렬화하려면 해당 클래스에서**

**명시적으로 설정하는 게 좋다.**

```java
import java.io.Serializable;

public class StudentDTO implements Serializable {
    private static final long serialVersionUID = 1L;
    
    private String name;
    private int rollNumber;

    public StudentDTO(String name, int rollNumber) {
        this.name = name;
        this.rollNumber = rollNumber;
    }

    // Getter and setter methods
}
```

**주의** 

> **일반적으로 serialVersionUID는 컴파일러에 의존한다. 컴파일러가 자동으로 할당해 준다.**   
> **문제는 송/수신 서버의 컴파일러 버전이 다르게 되면, InvalidClassException 이 발생할 수 있다.**
