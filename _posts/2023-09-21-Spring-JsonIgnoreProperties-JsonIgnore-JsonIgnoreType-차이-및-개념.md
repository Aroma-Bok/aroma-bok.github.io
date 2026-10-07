---
title: "[Spring] @JsonIgnoreProperties, @JsonIgnore, @JsonIgnoreType 차이 및 개념"
date: 2023-09-21 12:53:51 +0900
categories: ["Language", "Java"]
tags: ["@jsonignore", "@jsonignoreproperties", "@jsonignoreproperties @jsonignoretype @jsonignore 차이 및 개념", "@jsonignoretype", "@jsonproperty"]
tistory_url: "https://aroma-bok.tistory.com/entry/Spring-JsonIgnoreProperties-JsonIgnore-JsonIgnoreType-%EC%B0%A8%EC%9D%B4-%EB%B0%8F-%EA%B0%9C%EB%85%90"
---
**개발하다 보면 @JsonIgnoreProperties, @JsonIgnore, @JsonIgnoreType 어노테이션을 보게 될 것이다. 제대로 개념을 잡고 왜 사용하는지 개념을 정리해보려고 한다.**

* * *

**3가지 모두 다 Java의 Jackson 라이브러리와 관련이 있으며, JSON 직렬화 및 역직렬화 작업에서 사용되는 인기 있는**

**라이브러리이다.**

**직렬화 및 역직렬화 개념에 대해 알고 싶다면 전에 작성해 둔 내용을 참고하자.**

**[2023.09.12 - \[Language/Java\] - \[Java\] serialVersionUID](https://aroma-bok.tistory.com/entry/Java-serialVersionUID)**

* * *

### **@JsonIgnoreProperties**

-   **역할 : 클래스 수준에서 사용되며, Jackson이 Java 객체를 Json으로 직렬화 또는 Json을 Java 객체로** **역직렬화 작업을 수행할 때 특정 필드를 무시하도록 지정**
-   **사용 이유 : 클래스의 일부 필드를 Json으로 직렬화하거나 Java 객체로 역직렬화할 때 무시하고 싶은 경우에 사용**

```java
import com.fasterxml.jackson.annotation.JsonIgnoreProperties;

@JsonIgnoreProperties({"field1", "field2"})
public class MyObject {
    private String field1;
    private int field2;
    private String field3;

    // 생성자, 게터 및 세터 메서드 생략

    // 이 클래스의 필드 중 "field1"과 "field2"는 JSON 직렬화 및 역직렬화 시 무시됩니다.
}
```

**MyObject 클래스에 적용되어 field1과 field2 필드가 Json 직렬화 및 Java 역직렬화 작업에서 무시된다.**

#### **@JsonIgnoreProperties 속성**

1.  **value : 무시할 필드나 메서드의 이름을 문자열 배열로 지정**
2.  **allowGetters : 기본적으로 false, true로 설정하면 Getter 메서드가 있는 필드를 무시하지 않도록 허용,**  
    **즉 직렬화는 허용, 역직렬화는 허용하지 않음**
3.  **allowSetters : 기본적으로 false, true로 설정하면 Setter 메서드가 있는 필드를 무시하지 않도록 허용,**  
    **즉, 역직렬화는 허용, 직렬화는 허용하지 않음**
4.  **ignoreUnknown : 기본적으로 false, true로 설정하면 알려지지 않은 속성이나 필드를 무시,**  
    **즉, Json을 Java로 역직렬화하는 과정에서 Java의 VO에 없는 속성에 대해서 무시 (에러 발생 예방)**

```java
import com.fasterxml.jackson.annotation.JsonIgnoreProperties;

@JsonIgnoreProperties(value = {"field1", "field2"}, allowGetters = true, ignoreUnknown = true)
public class MyObject {
    private String field1;
    private int field2;
    private String field3;
    
    // 생성자, 게터 및 세터 메서드 생략

    // 이 클래스에서 "field1"과 "field2"를 무시하고, 게터 메서드로 값에 접근 허용, 알려지지 않은 속성 무시
}
```

```java
ObjectMapper objectMapper = new ObjectMapper();
MyObject myObject = new MyObject("Value1", 42, "Value3");
String json = objectMapper.writeValueAsString(myObject);
```

**json 문자열에는 field1과 field2 필드의 값이 포함되어 있지 않다.**

```java
String json = "{\"field1\":\"Value1\",\"field2\":42,\"field3\":\"Value3\"}";
MyObject myObject = objectMapper.readValue(json, MyObject.class);
```

**반대로 Java로 역직렬화해도 field1과 field2 필드의 값이 포함되어 있지 않다.**

* * *

### **@JsonIgnore**

-   **역할 : 필드 레벨 수준에 사용되며, 해당 필드가 Json 직렬화 및 Java로 역직렬화 작업에서 무시되도록 지정**
-   **사용 이유 : 특정 필드를 Json에서 숨기고자 할 때 사용 (Ex: 비밀번호, 토큰, 등 민감한 데이터 숨길 때)**

```java
import com.fasterxml.jackson.annotation.JsonIgnore;

public class MyObject {
    private String field1;
    private int field2;
    
    @JsonIgnore
    private String field3;

    // 생성자, 게터 및 세터 메서드 생략

    // 이 클래스의 "field3" 필드는 JSON 직렬화 및 역직렬화 시 무시됩니다.
}
```

**field3 필드에 @JsonIgnore가 적용되어 Json 직렬화 및 역직렬화 작업에서 무시된다.**

* * *

#### **@JsonIgnoreType**

-   **역할 : 클래스 레벨(필드 및 메서드)에 적용되며, 해당 클래스의 모든 멤버가 Json 직렬화 및 Java로 역직렬화**  
    **작업에서 무시되도록 지정**
-   **사용 이유 : 특정 클래스와 해당 멤버를 완전히 Json에서 숨기고자 할 때 사용**

```java
import com.fasterxml.jackson.annotation.JsonIgnoreType;

@JsonIgnoreType
public class MyIgnoredType {
    private String field1;
    private int field2;
    
    // 생성자, 게터 및 세터 메서드 생략
}

public class MyObject {
    private String field3;
    private MyIgnoredType ignoredField;

    // 생성자, 게터 및 세터 메서드 생략

    // MyIgnoredType 클래스에 @JsonIgnoreType 어노테이션이 적용되어 있으므로,
    // MyIgnoredType 객체는 JSON 직렬화 및 역직렬화 시 무시됩니다.
}
```

**@JsonIgnoreType은 MyIgnoredType 클래스에 적용되어 있으므로 모든 멤버(필드 및 메서드)는 Json 직렬화 및 역직렬화 작업에서 무시된다.**

**따라서 MyObject 클래스의 ignoredField 필드가 MyIgnoredType  객체를 가지더라도 이 객체는 Json으로 직렬화하거나 Java로 역직렬화되지 않는다.**

* * *

**정리하다 보니까 @JsonProperty에 대한 개념도 있어서 같이 정리해 본다.**

### **@JsonProperty**

-   **역할 : Java 객체와 Json 데이터 간의 필드 이름 매핑을 지정할 때 사용**
-   **사용 이유 : 기본적으로 Jackson은 Java 클래스의 필드 이름과 Json 데이터 필드 이름이 동일하다고 가정한다.**  
    **그러나 경우에 따라 달라질 수 있는 상황에 대비하여 @JsonProperty 어노테이션을 사용하여 매핑을 명시적으로  
    정의할 수 있다.**
-   **사용 방법 :**   
    **1) 필드에 매핑**  
    **2) Getter 메서드에 매핑**

**필드에 매핑**

```java
import com.fasterxml.jackson.annotation.JsonProperty;

public class MyObject {
    @JsonProperty("customField1") // JSON에서는 "customField1" 로사용
    private String field1;
    
    @JsonProperty("customField2") // JSON에서는 "customField2" 로사용
    private int field2;

    // 생성자, 게터 및 세터 메서드 생략
}
```

**위의 예시 코드에서는 field1을 Json 데이터의 customField1로, field2 필드를 Json 데이터의 customField2로 매핑.**

**Getter 메서드에 매핑**

```java
public class Person {
    private String fullName;
    private int age;

    @JsonProperty("full_name") // JSON에서는 "full_name"으로 사용
    public String getFullName() {
        return fullName;
    }

    @JsonProperty("age_in_years") // JSON에서는 "age_in_years"으로 사용
    public int getAge() {
        return age;
    }

    // 생성자와 다른 메서드는 생략
}
```

**위의 예시 코드에서는 getFullName 메서드를 Json 데이터의 full\_name로, **getAge** 메서드를 Json 데이터의 age\_in\_years로 매핑.**
