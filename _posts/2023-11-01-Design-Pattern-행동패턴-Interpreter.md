---
title: "[Design Pattern] 행동패턴 - Interpreter"
date: 2023-11-01 15:24:28 +0900
categories: ["Language", "Design Pattern"]
tags: ["designpattern interpreter", "designpattern interpreter java", "java interpreter", "디자인패턴 인터프리터", "자바 인터프리터"]
tistory_url: "https://aroma-bok.tistory.com/entry/Design-Pattern-%ED%96%89%EB%8F%99%ED%8C%A8%ED%84%B4-Interpreter"
---
### **인터프리터 (Interpreter) 패턴**

-   **자주 등장하는 문제를 간단한 언어로 정의하고 재사용하는 패턴**
-   **인터프리터의 목적은 자주 사용되는 특정한 문법적 규칙이 존재한다면, 이를 일련의 규칙을 통해 언어/문법으로**
-   **규격화하여 해석하는 목적을 가지는 패턴 (e.g : 정규표현식)**

#### **구조**

-   **Context : Expression에서 사용하는 모든 데이터들이 저장되어 있는 공간**
-   **Expression : 일련의 규칙을 계산하여 결과값을 반환**
-   **TerminalExpression : Expression을 포함하지 않고, 계산된 결과를 반환**
-   **(종료를 포함 - 더 이상 다른 문자로 치환될 수 없는 종점 문자를 의미)**
-   **NonTerminalExpression  : Expression을 참조하여, 종료를 하지 않고 다음 규칙으로 값을 넘기는 역할**
-   **(다른 문자로 치환)**

![](/assets/img/posts/243/1.png)

  
**인터프리터 (Interpreter) 패턴은 NonTerminalExpression을 통해 해석한 문자를 다음 해석이 가능한 또 다른 Expression으로 넘긴다. 그리고 TerminalExpression만 남으면, 해석된 마지막 문자를 반환하여 Interpreter로써의  
기능을 수행하는 구조를 가진다. 즉, 트리(Tree) 구조를 가진다.**  
 

* * *

**인터프리터 (Interpreter) 패턴 사용 전**

-   **후위표기법을 코드로 표현할 것이다**
-   **후위표기법은, 중위 연산자 (2 - 3 = -1)와 달리 연산자가 나오면 앞의 두 숫자를 연산하는 형식 (2 3 - = -2)**
-   **'123+-'이란 형식에 대한 연산을 자주 반복해서 사용하는 경우라면 'xyz+-'라는 하나의 형식으로**
-   **지정하여 문법으로 지정할 수 있다.**

```java
public class PostfixNotation {
    private final String expression;

    public PostfixNotation(String expression) {
        this.expression = expression;
    }

    public void calculate() {
        Stack<Integer> numbers = new Stack<>();

        for(char c : this.expression.toCharArray()) {
            switch (c) {
                case '+' :
                    numbers.push(numbers.pop() + numbers.pop());
                    break;
                case '-' :
                    int right = numbers.pop();
                    int left = numbers.pop();
                    numbers.push(left - right);
                    break;
                default: numbers.push(Integer.parseInt(c + ""));
            }
        }
        System.out.println(numbers.pop());
    }
}
```

```java
    @Test
    void test1() {
        PostfixNotation postfixNotation = new PostfixNotation("123+-");
        postfixNotation.calculate();
    }
```

**이를 인터프리터(Interpreter) 패턴을 사용하여 적용해 보자.**

* * *

**인터프리터 (Interpreter) 패턴 사용 후**

-   **x면 x, y면 y, z면 z를 반환하면 되는 TerminalExpression이 되어 종료를 포함**
-   **'+-'과 같은 Expression은 다른 Expression을 Interpreter 한 다음 그 결과를 연산하는 NonTerminalExpression**

-   **인터프리터 기능을 추상화한 Expression 클래스**
-   **이 인터페이스의 구현체를 TerminalExpression과 NonTerminalExpression의 성질을 담은 구현체로 기능을 캡슐화**

```java
@AllArgsConstructor
public class PlusExpression implements PostfixExpression {

    private PostfixExpression left;
    private PostfixExpression right;

    @Override
    public int intepret(Map<Character, Integer> context) {
        return left.intepret(context) + right.intepret(context);
    }
}
```

-   **TerminalExpression 구조를 가지는 VariableExpression 클래스**
-   **TerminalExpression 이기 때문에, 또 다른 Expression을 반환하지 않고 종점(문자)을 반환**

```java
public class VariableExpression implements PostfixExpression {
    private Character variable;

    public VariableExpression(Character variable) {
        this.variable = variable;
    }

    @Override
    public int intepret(Map<java.lang.Character, Integer> context) {
        return context.get(variable);
    }
}
```

-   **NonTerminalExpression 구조를 가지는 연산자 클래스**
-   **NonTerminalExpression이기 때문에 다음 표현식인 Expressoin을 반환**

```java
@AllArgsConstructor
public class PlusExpression implements PostfixExpression {

    private PostfixExpression left;
    private PostfixExpression right;

    @Override
    public int intepret(Map<Character, Integer> context) {
        return left.intepret(context) + right.intepret(context);
    }
}
```

```java
@AllArgsConstructor
public class MinusExpression implements PostfixExpression {

    private PostfixExpression left;
    private PostfixExpression right;

    @Override
    public int intepret(Map<Character, Integer> context) {
        return left.intepret(context) - right.intepret(context);
    }
}
```

-   **Client : Expresson을 통해 값을 주입하여 결과를 얻는다.**
-   **Parse : Expresson을 반환하는 별도의 Parser 클래스가 존재하는 이유는 클라이언트가 입력한 특정 규칠을**  
    **지정된 기능 (후위연산)을 수행하는 문법으로 변환하기 위함**

```java
public class PostfixParser {
    public static PostfixExpression parse(String expression) {
        Stack<PostfixExpression> stack = new Stack<>();
        for(char c : expression.toCharArray()) {
            stack.push(getExpression(c,stack));
        }
        return stack.pop();
    }

    private static PostfixExpression getExpression(char c, Stack<PostfixExpression> stack) {
        switch (c) {
            case '+': return new PlusExpression(stack.pop(), stack.pop());
            case '-' :
                PostfixExpression right = stack.pop();
                PostfixExpression left = stack.pop();
                return new MinusExpression(left,right);
            default: return new VariableExpression(c);
        }
    }
}
```

* * *

#### **인터프리터 (Interpreter) 패턴**

#### **장점**

-   **각 문법 규칙을 클래스로 표현하기 때문에 언어를 쉽게 구현**
-   **문법이 클래스에 의해 표현되기에 언어를 쉽게 변경하거나 확장 가능**
-   **클래스 구조에 메소드만 추가하면 프로그램을 해석하는 기본 기능 외에 보기 쉽게 출력하는 기능이나**  
    **더 나은 프로그램 확인 기능 같은 새로운 기능을 추가할 수 있다.**

#### **단점**

-   **문법 규칙의 개수가 많아지면 복잡해진다.**
-   **다양한 문법이 생성될 때 성능 저하가 발생한다.**
