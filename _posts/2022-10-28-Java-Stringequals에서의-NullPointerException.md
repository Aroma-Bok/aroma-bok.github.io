---
title: "[Java] String.equals()에서의 NullPointerException"
date: 2022-10-28 15:42:12 +0900
categories: ["Language", "Java"]
tags: ["equals()에서의 nullpointerexception", "nullpointerexception"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-Stringequals%EC%97%90%EC%84%9C%EC%9D%98-NullPointerException"
---
**개발을 하면서 String 클래스의 equals 메서드를 종종 사용한다. equlas() 메서드에서 NullPointerException이 터질 줄은 정말 몰랐다. 로그를 확인해보니 equlas()에서 예외가 터지는 것을 보고 후다닥 수정을 한 경험이 있다.**

**아래 설명처럼, 비교할 객체를 받아서 true, false를 리턴한다.**

```java
public boolean equals(Object obj)

Parameters:
obj - the reference object with which to compare.
Returns:
true if this object is the same as the obj argument; false otherwise.
```

**아래는 필자의 소스이다. 일반 회원 가입하고 이 소스를 타면 문제없이 흘러간다.**

**하지만 카카오로 회원 가입한 사용자는 uaYn 값이 null로 들어가 버려서, 조건문에서 'NullPointerException' 이 터진다.**

```java
String uaYn = member.getUaYn();  // 임직원인지 체크

if(uaYn.equals("Y")) { // 임직원이면 '(임직원)'으로 표시
    StringBuffer privacyName = new StringBuffer(userName);

    privacyName.append("(임직원)");

    model.addAttribute("userName", privacyName);

} else { // 임직원이 아니면 그대로 이름 표시
    model.addAttribute("userName", userName);
}
```

**그래서 아래처럼 한번 더 'null' 값을 체크해주는 조건문으로 처리했다.**

```java
if(!"".equals(uaYn) && uaYn != null) {
    if(uaYn.equals("Y")) { // 임직원이면 '(임직원)'으로 표시
        StringBuffer privacyName = new StringBuffer(userName);

        privacyName.append("(임직원)");

        model.addAttribute("userName", privacyName);

    } else { // 임직원이 아니면 그대로 이름 표시
        model.addAttribute("userName", userName);
    }
}
```

**equals() 메서드를 사용할 때 null체크를 꼭 잘하자.**
