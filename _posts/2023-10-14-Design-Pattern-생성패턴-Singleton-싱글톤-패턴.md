---
title: "[Design Pattern] 생성패턴 - Singleton (싱글톤 패턴)"
date: 2023-10-14 12:45:22 +0900
categories: ["Language", "Design Pattern"]
tags: ["singleton 코드", "디자인패턴", "디자인패턴 코드", "백기선 싱글톤", "싱글톤", "싱글톤 코드", "싱글톤 패턴"]
tistory_url: "https://aroma-bok.tistory.com/entry/Design-Pattern-%EC%83%9D%EC%84%B1%ED%8C%A8%ED%84%B4-Singleton-%EC%8B%B1%EA%B8%80%ED%86%A4-%ED%8C%A8%ED%84%B4"
---
#### **Singleton (싱글톤 패턴)**

-   **인스턴스를 오직 한 개만 제공하는 클래스**

#### **싱글톤 패턴을 사용하는 이유**

1.  **시스템 런타임, 환경 세팅에 대한 정보 등, 인스턴스가 여러 개 일 때 문제가 생길 수 있는 객체들을 한 곳에서 제어하기 위해서이다.**
2.  **싱글톤 패턴은 2가지 목적을 가지고 있다.**  
    **1) 인스턴스를 오직 1개만 만들어야 한다. (한 곳에서만 제어하기)**  
    **2) 만든 인스턴스에 글로벌하게 접근하는 방식을 제공해야 한다.**

#### **싱글톤 패턴을 구현하는 방법**

-   **싱글톤을 구현할 때 신경 써야 하는 2가지**  
    **1) App Run 시, 즉시 객체 인스턴스를 생성해 줄 것인가? - Eager Initialization**  
    **2) 객체의 호출 시, 객체 인스턴스를 생성해 줄 것인가? - Lazy Initialization**

**1\. private 생성자에 static 메소드**

-   **가장 간단하고 보편적인 방법이지만, 수많은 스레드가 동시에 생성 체크(if) 문에 접근 시 객체 생성 체크를  
    통과하기 때문에, 멀티스레드 환경에서는 안전하지 않은 방법**
-   **객체를 호출할 때 인스턴스를 생성하는 Lazy Initialization 방식**
-   **Instance 호출 시 만들어진 Instance 가 있다면 반환해 주고 없다면 생성**

```java
public class SettingsPrivateStatic {
    private static SettingsPrivateStatic instance;

    private SettingsPrivateStatic() {

    }

    public static SettingsPrivateStatic getInstance() {
        if(instance == null){
            instance = new SettingsPrivateStatic();
        }
        return instance;
    }
}
```

> **1\. 생성자를 private으로 만든 이유?**  
> **\> 외부에서의 객체 생성을 막고, 한 곳에서 instance를 관리하기 위함**  
>   
> **2\. getInstance를 static으로 선언한 이유?**  
> **\> 외부에서 글로벌하게 만들어진 객체에 접근하는 방법이 필요하기 때문**  
>   
> **3\. 그렇다면 getInstance()이 멀티스레드 환경에서 안전하지 않은 이유는?**  
> **\> 객체의 생성 전, 동시 다발적으로 생성체크 박스에 스레드가 접근하게 되면, 올바른 객체 생성 체크가 이루어지지 않을 수 있기 때문**

* * *

**2\. 동기화(Synchronized)를 사용해 멀티스레드 환경에서도 안전한 싱글톤 만들기**

```java
public class SettingsSynchronized {
    private static SettingsSynchronized instance;

    public static synchronized SettingsSynchronized getInstance() {
        if(instance == null) {
            instance = new SettingsSynchronized();
        }
        return instance;
    }
}
```

> **Synchronized of Java**  
> **data의 Thread-Safe를 하기 위해서 자바에서는 synchronized 키워드를 제공해 스레드 간 동기화를 시켜**  
> **data의 Thread-safe를 가능케 한다.**  
>   
> **synchronized로 지정된 임계영역은 한 스레드가 이 영역에 접근하여 사용할 때는 lock이 걸림으로써**  
> **다른 스레드가 접근할 수 없게 된다.**

> **1\. 자바의 동기화 블록 처리 방법은?**  
> **\> 해당 메서드에 synchronized 키워드를 붙여주면, 메소드 전체가 임계영역으로 설정된다.**  
> **임계영역으로 설정된 부분은 해당 메소드를 포함한 객체의 lock을 걸었다가 메소드가 종료되면 unlock 된다.**  
>   
> **2\. getInstance() 메소드 동기화 시 사용하는 lock은 인스턴스의 lock인가? 클래스의 lock인가?**  
> **\> synchronized 키워드는 객체 단위의 lock을 한다. 그 이유는 동기화를 하는 이유가 인스턴스 변수의 조작을**   
> **순차적으로 하기 위함인데 객체 단위의 lock을 하지 않으면 synchronized가 걸리지 않은 다른 함수에서 해당 값을 조작하거나 조회할 경우 동기화하는 이유가 없어지기 때문이다.**

* * *

**3\. 이른 초기화 (Eager Initialization)을 사용하는 방법**

-   **멀티스레드 환경에서 안전한 싱글톤의 2번째 방법**
-   **아래 2가지 상황에서 유용한 방법**  
    **1) 객체를 생성하는 비용이 적을 때**  
    **2) 객체의 생성을 나중에 하지 않아도 될 때**

```java
public class SettingsEagerInitialization {
    private static final SettingsEagerInitialization instance = new SettingsEagerInitialization();

    private SettingsEagerInitialization() {};

    public static SettingsEagerInitialization getInstance() {
        return instance;
    };
}
```

**생성자에서 checked 예외를 던지는 경우**

```java
public class SettingsEagerInitialization {
    private static SettingsEagerInitialization instance = new SettingsEagerInitialization();

    static {
        try {
            instance = new SettingsEagerInitialization();
        }catch (Exception e){
            e.printStackTrace();
        }
    }
    
    private SettingsEagerInitialization() {};

    public static SettingsEagerInitialization getInstance() {
        return instance;
    };
}
```

> **1\. 이른 초기화가 단점이 될 수도 있는 이유?**  
> **\> 단점은 앱 구동 시 미리 만든다는 것.**  
> **\> 객체의 생성비용이 큰데, 사용하지 않게 된다면 앱을 구동시킬 때, 너무 많은 리소스 자원을 사용하기 때문이다.**  
>   
> **2\. 만약에 생성자에서 checked 예외를 던진다면 이 코드를 어떻게 변경해야 할까요?**  
> **\> 기본적으로 함수에서 checked exception을 던지면 함수를 호출하는 쪽에서 try~catch문으로 감싸야한다.**  
> **하지만 예제처럼 변수를 초기화하는 과정에서 try~catch문을 사용할 수 없다. 그럴 경우에는 static 블록을 이용해서 instance를 초기화하면 되는데 이 경우 final 키워드를 사용할 수 없다.**  
>   

* * *

**4\. double checked locking으로 효율적인 동기화 블록 만들기**

-   **멀티스레드 환경에서 지연 초기화(Lazy Initialization)를 사용하고 싶을 때, double checked locking으로 효율적인**  
    **동기화 블록을 만들 수 있다.**
-   **if에 걸친 후, 동기화를 진행하기 때문에, 위의 방식보다는 성능 이점이 존재하는 방법**
-   **volatile 키워드를 사용**

```java
public class SettingsDoubleCheckSynchronized {
    private static volatile SettingsDoubleCheckSynchronized instance;

    private SettingsDoubleCheckSynchronized() {};

    public static SettingsDoubleCheckSynchronized getInstance() {
        if(instance == null) {
            synchronized (SettingsDoubleCheckSynchronized.class) {
                if(instance == null) {
                    instance = new SettingsDoubleCheckSynchronized();
                }
            }
        }
        return instance;
    };
}
```

> **1\. double checked locking이라고 부르는 이유?**  
> **\> checking을 2번 했기 때문이다. 2개의 스레드가 동시에 if문을 통과했다 하더라도 synchronized 때문에 하나의 스레드만 안으로 들어갈 수 있다. 그리고 인스턴스를 생성하고 빠져나가는데 이때, 다른 if문을 통과한 스레드가 synchronized로 들어오더라도 인스턴스가 이미 생성되었기에 if문에서 튕겨 나간다.**  
>   
> **2\. instance 변수는 어떻게 정의해야 하는가? 그 이유는?**  
> **\> 변수에 volatile 키워드를 붙인다. volatile을 사용하지 않은 변수는 성능향상을 위해 CPU 캐시에 저장한다.**  
> **이 경우, 스레드가 변수값을 읽어올 때 각각의 CPU의 캐시에서 가져오기 때문에 값이 달라 값의 불일치가 발생.**

* * *

**5. static inner 클래스를 사용하는 방법**

-   **백기선 님이 권장하는 방법 중 1개**
-   **멀티스레드 환경에서도 안전하고, 호출될 때, instance가 만들어지는 Lazy Initialization 방식**

```java
public class SettingsStaticInner {
    private SettingsStaticInner() {};

    private static class SettingHolder {
        private static final SettingsStaticInner INSTANCE = new SettingsStaticInner();
    }

    public static SettingsStaticInner getInstance() {
        return SettingHolder.INSTANCE;
    }
}
```

> **1\. 이 방법은 static final을 썼는데도 왜 지연 초기화 (Lazy Initialization)라고 볼 수 있는가?**  
> **\> static 필디는 클래스가 처음 로딩될 때 정적인 메모리 공간에 만들어지는데, holder가 가지고 있는 클래스가 로딩되는 시점에 getInstance()를 호출할 때 로딩되기 때문에 지연 초기화 (Lazy Initialization)라고 본다.**

> **기본 사전 지식**  
> **1\. 인스턴스 초기화 블록 (static 사용 x)**  
> **\> 객체 생성할 때마다 블록이 실행**  
> **\> 부모 생성자 이후에 실행**  
> **\> 생성자(자신)보다 먼저 실행**  
>   
> **2\. 정적 초기화 블록 (static사용)**  
> **\> 클래스 로드 시 한 번만 실행**

* * *

#### **싱글톤 패턴을 깨뜨리는 방법**

-   **싱글톤으로 만들었음에도 불구하고 자바에서 제공하는 다양한 방법들을 이용해, 싱글톤을 깨트릴 수 있다.**

**기본적으로 싱글톤은, 아래 Test 코딩처럼 호출할 때마다 같은 객체를 반환한다.**

![](/assets/img/posts/228/1.png)

*2개 테스트 진행 결과 동일 인스턴*

**1\. 리플렉션**

-   **자바에서 제공하는 리플렉션 API를 사용해서 내부 설정을 변경할 수 있다.**
-   **리플렉션으로 생성자를 가져와 접근제어자에 대한 설정을 변경해 Singleton으로 구현한 객체를 새로운 인스턴스로 생성할 수 있는 방법.**

```java
    @Test
    @DisplayName("싱글톤 깨트리는 방법1 - 리플레션")
    void Reflection() throws Exception {
        SettingsStaticInner setting = SettingsStaticInner.getInstance();

        Constructor<SettingsStaticInner> newSetting = SettingsStaticInner.class.getDeclaredConstructor();
        newSetting.setAccessible(true);

        SettingsStaticInner setting2 = newSetting.newInstance();

        assertThat(setting).isEqualTo(setting2);
    }
```

![](/assets/img/posts/228/2.png)

-   **class.getDeclaredConstructor() : 해당 클래스 객체가 나타내는 클래스 또는 인터페이스의 지정된 생성자를 반영하는 Constructor 객체를 반환한다.**
-   **setAccessible(true) : 해당 객체에 대해 Java 언어 Access 제어에 대한 검사를 억제해야 함을 나타낸다.**  
    **즉, 접근제어를 무시함을 나타낸다. false인 경우에는 접근제어의 억제를 하지 않겠다는 의미.**
-   **newInstance() : 이 Constructor 객체가 나타내는 생성자를 사용하여 지정된 초기화 매개 변수를 사용하여 생성자의 새로운 인스턴스를 만들고 초기화한다.**

> **해당 객체의 생성자를 불러온다 > 접근제어를 푼다 > 새로운 인스턴스를 생성한다 > 싱글톤 패턴이 깨진다.**

> **1\. 리플렉션?**  
> **\> 리플렉션은 구체적인 클래스의 타입을 몰라도 안에 선언되어 있는 함수, 변수들에 접근할 수 있게 해주는**  
> **자바의 API**  
>   
> **2.setAccessible(true)를 사용하는 이유?**  
> **\> 기본 생성자는 이 예제에서 private으로 선언되어 있다. 즉, 외부에서는 호출할 수 없다는 의미.**  
> **setAccessible(true)를 통해 접근제어자에 대한 억제를 함으로써 기본생성자를 사용가능하게 한다.**  
> **그리고 newInstance()를 사용해 새로운 객체를 만들 수 있게 한다.**

* * *

**2\. 직렬화 및 역직렬화의 사용**

-   **객체를 직렬화시킨 후 다시 역직렬화시켜서 새로운 객체를 만드는 방법**
-   **자바에서 외부 파일을 역직렬화할 시에는, 반드시 생성자를 사용하여 새로운 인스턴스를 만든다.**
-   **이러한 설정을 이용해서, Singleton 설정을 깨트릴 수 있다.**

```java
    @Test
    @DisplayName("싱글톤 깨트리는 방법2 - 직렬화 & 역직렬화 이용")
    void SerializeAndRevers() throws Exception{
        SettingsStaticInner setting = SettingsStaticInner.getInstance();
        SettingsStaticInner setting2 = null;

        try(ObjectOutput out = new ObjectOutputStream(new FileOutputStream("settings.obj"))) {
            out.writeObject(setting);
        }

        try(ObjectInput input = new ObjectInputStream(new FileInputStream("settings.obj"))) {
            setting2 = (SettingsStaticInner) input.readObject();
        }

        assertThat(setting).isEqualTo(setting2);
    }
```

![](/assets/img/posts/228/3.png)

> **참고 : 직렬화하려는 객체 클래스에 대해서 implements Serializable 은 필수!**  
> **하지 않으면 NotSerializableException 발생**

> **1\. 자바의 직렬화 & 역직렬화**  
> **\> 직렬화는 자바 시스템 내부에서 사용되는 Object 또는 Data를 외부의 자바 시스템에서도 사용할 수 있도록 byte 형태로 변환하는 기술이며, 역직렬화는 byte로 변환된 Data를 원래대로 Object나 Data로 변환하는 기술.**  
>   
> **2\. Serializable Id?**  
> **\> Serializable를 상속받은 경우 클래스의 버전관리를 위해서 serialVersionUID 변수를 명시적으로 선언해 주지 않으면 컴파일러가 계산한 값을 부여하는데, Serializable Class 또는 Outer Class에 변경이 있으면 serialVersionUID값이 바뀌게 된다. 만약 Serialize 할 때와 Deserialize 할 때의 serialVersionUID 값이 다르면 InvalidClassExcepions가 발생하여 저장된 값을 객체로 변환할 수 없다.**  
>   
> **3\. try-resource 블록**  
> **\> 기존 try-catch-finally 블록에서 사용하고 꼭 종료해줘야 하는 resource를 사용할 때 final 블럭에서 resource를 해제하는데, try-resource 블록을 사용하면 따로 명시적으로 resource 해제해주지 않아도 자동으로 해제해 준다.**

**직렬화, 역직렬화 대응방법**

-   **역직렬화 시 생성자 사용 문제에 대한 해결방법은, "readResolve()" 메소드를 객체 안에 재정의 하는 것**
-   **ObjectInputStream에 대한 공식문서를 보면, "역직렬화된 객체는 다른 당사자에게 표시되는 객체를 반환하는 readResolve 메소드를 정의하거나 readUnshared가 스트림의 다른 곳에서 또는 외부 수단을 통해 얻을 수 있는 클래스개체 또는 열거형 상수를 반환할 수 있다"라고 나온다.**
-   **역직렬화 시, readObejct보다 readResolve를 먼저 호출하게 되어 있기 때문에 만들어 둔 instance를 반환하도록 해주면 된다.**

```java
public class SettingsStaticInner implements Serializable {
    SettingsStaticInner() {};

    private static class SettingHolder {
        private static final SettingsStaticInner INSTANCE = new SettingsStaticInner();
    };

    public static SettingsStaticInner getInstance() {
        return SettingHolder.INSTANCE;
    };

    //직렬화 및 역질렬화시, 새로운 인스턴스를 만드는 것에 대한 방지
    private Object readResolve() {
        return SettingHolder.INSTANCE;
    }
}
```

![](/assets/img/posts/228/4.png)

*같은 객체를 사용*

* * *

**3\. 리플렉션의 대응방법 - Eunm Singleton \[안전하고 단순하게 구현하는 방법\]**

-   **Enum을 사용한다면 리플렉션에 대응할 수 있다.**
-   **내부 바이트코드를 보게 되면 Enum class는 리플렉션에 대해서도 막아 놓았기 때문에 안전하다.**
-   **다만, Enum은 앱 로딩 시 메모리에 할당되기 때문에 지연 초기화하는 할 수 없다.**

```java
public enum EnumSingleton {
    INSTANCE
}
```

```java
    @Test
    @DisplayName("싱글톤을 깨트리는 방법3 - Enum을 이용한 Singleton")
    void EnumSingleton() throws Exception {
        EnumSingleton setting = EnumSingleton.INSTANCE;
        EnumSingleton setting2 = null;

        Constructor<?>[] newSettings = EnumSingleton.class.getDeclaredConstructors();
        for(Constructor<?> newSetting : newSettings) {
            newSetting.setAccessible(true);
            setting2 = (EnumSingleton) newSetting.newInstance("INSTANCE");
        }

        assertThat(setting).isEqualTo(setting2);
    }
```

![](/assets/img/posts/228/5.png)

*Enum 클래스는 리플렉션으로 newInstace 할 수 없다.*

> **1\. Enum 타입의 인스턴스를 리플렉션을 통해 만들 수 있나?**  
> **\> 없다**  
>   
> **2\. Enum으로 싱글톤 타입을 구현할 때의 단점은?**  
> **\> 인스턴스를 미리 생성하기에 상속이 불가능하다.**  
>   
> **3\. 직렬화 & 역직렬화 시에도 별도로 구현해야 하는 메소드가 있는가?**  
> **\> Enum은 기본적으로 Enum이라는 클래스를 상속받고 있고 이 클래스는 이미 Serializable을 상속하고 있기 때문에 Enum도 별다른 안전장치를 마련하지 않아도 직렬화 & 역직렬화가 가능하다.**

* * *

#### **싱글톤 (Singleton) 패턴 복습**

-   **자바에서 Eunm을 사용하지 않고 싱글톤 패턴을 구현하는 방법은?**
-   **private 생성자와 static 메소드를 사용하는 방법의 단점은?**
-   **enum을 사용해 싱글톤 패턴을 구현하는 방법의 장점과 단점은?**
-   **static inner 클래스를 사용해 싱글톤 패턴을 구현하라.**

#### **싱글톤 (Singleton) 패턴은 실무에서 어떻게 사용하나?**

-   **스프링에서 빈의 스코프 중에 하나로 싱글톤 스코프**
-   **자바 java.lang.Runtime**
-   **다른 디자인패턴(빌더, 퍼사드, 추상팩토리 등) 구현체의 일부로 쓰이기도 한다**
