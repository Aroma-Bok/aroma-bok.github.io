---
title: "[Java] ObjectMapper vs MapStruct"
date: 2025-02-05 14:38:14 +0900
categories: ["Language", "Java"]
tags: ["mapstruct vs objectmapper", "mapstruct vs objectmapper 차이점"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-ObjectMapper-vs-MapStruct"
---
### **ObjectMapper**

-   **Jackson 라이브러리의 핵심 클래스**
-   ****주로 Java 객체와 JSON 간의 변환에 사용****

### **특징**  

1.  **JSON 직렬화 (Java 객체 -> JSON) 및 역직렬화 (JSON -> Java 객체) 수행**
2.  **리플렉션을 사용하여 런타임에 객체 매핑을 수행**
3.  **다양한 어노테이션을 통해 매핑 동작을 커스터마이즈 가능**

* * *

### **MapStruct**  

-   **Java 객체 간 매핑을 위한 코드 생성 도구**

### **특징**  

1.  **컴파일 시점에 매핑 코드를 생성**
2.  **주로 DTO와 Entity 간 변환에 사용**
3.  **어노테이션 기반으로 동작하며, 인터페이스 정의만으로 구현체를 자동 생성**

> **리플렉션(Reflection)**  
>   
> **Java 프로그래밍 언어의 중요한 기능 중 하나로, 실행 중인 Java 프로그램이 자기 자신을 검사하거나 내부의 속성을 조작할** **수 있게 해주는 기능.** **구체적인 Class Type을 알지 못하더라도 해당 Class의 method, type, variable들에 접근할 수 있도록 해주는 자바 API  
> **  
> **Reflection은 Runtime에 Class Type을 모르는 채로 객체를 생성하고 이용하기 때문에 동적 바인딩을 제공**  
> **MapStruct를 사용하면 객체 간 변환 작업을 효율 적으로 처리하고, 코드의 중복을 줄이며 유지보수성을 향상**

* * *

#### **예시 코드**

```java
public class Source {
    private String name;
    private int age;
    // getters and setters
}

public class Destination {
    private String name;
    private int age;
    // getters and setters
}
```

```java
@Mapper
public interface SourceDestinationMapper {
    SourceDestinationMapper INSTANCE = Mappers.getMapper(SourceDestinationMapper.class);
    
    Destination sourceToDestination(Source source);
}
```

#### **1\. MapStruct 사용 예시**

```java
Source source = new Source();
source.setName("John");
source.setAge(30);

Destination destination = SourceDestinationMapper.INSTANCE.sourceToDestination(source);
System.out.println(destination.getName()); // 출력: John
System.out.println(destination.getAge());  // 출력: 30
```

#### **2\. ObjectMapper 사용 예시**

```java
public class Person {
    private String name;
    private int age;
    // getters and setters
}

public class ObjectMapperExample {
    public static void main(String[] args) throws Exception {
        ObjectMapper mapper = new ObjectMapper();
        
        // Java 객체를 JSON 문자열로 변환
        Person person = new Person();
        person.setName("John");
        person.setAge(30);
        
        String jsonString = mapper.writeValueAsString(person);
        System.out.println(jsonString); // 출력: {"name":"John","age":30}
        
        // JSON 문자열을 Java 객체로 변환
        String json = "{\"name\":\"Jane\",\"age\":25}";
        Person result = mapper.readValue(json, Person.class);
        System.out.println(result.getName()); // 출력: Jane
        System.out.println(result.getAge());  // 출력: 25
    }
}
```

**이 예시들에서 볼 수 있듯이, MapStruct는 컴파일 시점에 생성된 매퍼를 통해 객체 간 변화를 수행하며,**

**ObjectMapper는 런타임에 리플렉션을 사용하여 JSON과 Java 객체 간의 변환을 수행**

* * *

#### **차이점**  

1.  **용도**  
    **ObjectMapper는 주로 Java 객체와 JSON 간 변환에 사용**  
    **MapStruct는 Java 객체 간 변환에 특화**
2.  **동작 시점**  
    **ObjectMapper는 런타임에 리플렉션을 사용하여 매핑을 수행**  
    **MapStruct는 컴파일 시점에 매핑 코드를 생성**
3.  **성능**  
    **MapStruct가 컴파일 시점에 코드를 생성하므로 일반적으로 더 높은 성능 제공**
4.  **유연성**  
    **ObjectMapper는 동적인 JSON 구조 처리에 더 유연**  
    **MapStruct는 정적인 객체 구조간 매핑에 더 적합**
5.  **사용 방식**  
    **ObjectMapper는 메서드 호출을 통해 직접 변환을 수행**  
    **MapStruct는 인터페이스 정의를 통해 매핑 규칙을 선언적으로 지정**

**결론적으로 ObjectMapper는 JSON 처리에 특화되어 있고, MapStruct는 Java 객체 간 효율적인 매핑에 중점**  

**프로젝트의 요구사항에 따라 적절한 도구를 선택하거나 두 도구를 함께 사용할 수 있다.**

**거대한 시스템을 만들다 보면, 다양한 도메인 영역들이 있고, 이를 변환하는 작업은 개발자에게 있어서 매우 귀찮고,**

**실수하기** **딱 좋고, 번거로운 경우가 굉장히 많다.**  

**이러한 실수를 하지 않게 검증된 라이브러리를 사용하는 것이 좋다.**
