---
title: "[Effective Java] 아이템11 - equals를 재정의하려거든 hashCode도 재정의하라"
date: 2024-02-20 14:16:42 +0900
categories: ["Language", "Effective Java"]
tags: ["effective java", "effective java hashcode", "effective java item 11", "equals를 재정의하려거든 hashcode도 재정의", "이펙티브 자바 아이템 11"]
tistory_url: "https://aroma-bok.tistory.com/entry/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C11-equals%EB%A5%BC-%EC%9E%AC%EC%A0%95%EC%9D%98%ED%95%98%EB%A0%A4%EA%B1%B0%EB%93%A0-hashCode%EB%8F%84-%EC%9E%AC%EC%A0%95%EC%9D%98%ED%95%98%EB%9D%BC"
---
**자바의 최고 조상 Object는 equals 메서드뿐만 아니라 hashCode 메서드도 기본적으로 정의되어 있다.**  
**HashMap, HashSet과 같은 Collection에서 Object의 hashCode 메서드를 활용하기 때문이다.**  
**그래서 Item10에서 Object를 재정의했다면, hashCode도 함께 재정의해야 한다.** 

* * *

  
**Object 명세서에서 정의되어 있는 hashCode가 지켜야 하는 규약이다.**

1.  **equals 비교에 사용되는 정보가 변경되지 않았다면, hashCode는 몇 번을 호출해도 일관되게 항상 같은**  
    **값을 반환해야 한다.**
2.  **equals가 두 객체를 같다고 판단했다면, 두 객체의 hashCode는 동일한 값을 반환해야 한다.**
3.  **equals가 두 객체를 다르다고 판단했더라도, 두 객체의 hashCode는 서로 다른 값을 반환할 필요는 없다.**  
    **다만, 다른 값을 반환해야 해시테이블의 성능이 좋아진다.**

* * *

**hashCode를 재정의를 안 하거나 잘못했을 때, 2번째 조항을 위반한다.**

**equals에서 논리적으로 같다고 판단하더라도 Object의 hashCode 기본 메서드는 이 둘을 전혀 다르게**

**판단할 수도 있다.**  
  
**테스트 결과, hashCode를 재정의해준다면 true 값을 반환하는 것을 확인할 수 있다.**

```java
package item11;

import java.util.Objects;

public class PhoneNumber {
    private final short areaCode, prefix, lineNum;

    public PhoneNumber(short areaCode, short prefix, short lineNum) {
        this.areaCode = areaCode;
        this.prefix = prefix;
        this.lineNum = lineNum;
    }

    private static short rangeCheck(int val, int max, String arg) {
        if(val < 0 || val > max) {
            throw new IllegalArgumentException(arg + ": " + val);
        }
        return (short) val;
    }

    @Override
    public boolean equals(Object o) {
        if(o == this) {
            return true;
        }
        if(!(o instanceof PhoneNumber)) {
            return false;
        }
        PhoneNumber pn = (PhoneNumber) o;
        return pn.lineNum == lineNum && pn.prefix == prefix && pn.areaCode == areaCode;
    }
    
    @Override
    public int hashCode() {
        int c = 31;
        int hashCode = Integer.hashCode(areaCode);
        hashCode = c * hashCode + Integer.hashCode(prefix);
        hashCode = c * hashCode + Integer.hashCode(lineNum);
        return hashCode;
    }
}
```

```java
    public static void  test1() {
        HashMap<PhoneNumber, String> map = new HashMap<>();
        PhoneNumber test1 = new PhoneNumber((short) 010, (short) 1234, (short) 5678);
        PhoneNumber test2 = new PhoneNumber((short) 010, (short) 1234, (short) 5678);
        System.out.println("첫 번째 test1의 hashCode : " + test1.hashCode());
        System.out.println("첫 번째 test2의 hashCode : " + test2.hashCode());
        map.put(test1, "테스트1");
        System.out.println(map.get(test2) == "테스트1"); // false -> true
        // hashCode를 재정의하지 않고 확인 - false
        // hashCode를 재정의하고 확인      - true
    }
```

* * *

#### **주의**

**아래와 같이 모두 같은 상수로 반환하면 HashTable의 성능이 O(1) -> O(N)으로 성능이 떨어지게 된다.**  
**물론 3번 정의에 위배되지는 않지만, 아래와 같이 설계하면 31이라는 키에 모든 아이템들이 순차적으로 쌓일 것이다. Array나 LinkedList에서 아이템을 검색하는 것과 다를 바가 없다.**  
  

```java
@Override
public int hashCode() {
    return 31;
}
```

* * *

#### **hashCode를 재정의할 때, 주의할 점**

1.  **성능을 높인다고 hashCode를 계산할 때, 핵심 필드를 생략해서는 안된다.**
2.  **hashCode 생성 규칙을 API 사용자에게 공표하지 말자. 해시 성능을 더 높일 수 있는 좋은 방안이 있는데 공표를 하게 되면,**   
    **hashCode를 믿고 작성한 코드들 때문에 다음 릴리즈 때 성능을 개선할 수 없게 된다.**

* * *

#### **정리**

**equals를 재정의 했다면, hashCode도 재정의하자.**

**좋은 해시 함수 만들기 방법을 통해 적적히 분배하여 성능을 높이자.**
