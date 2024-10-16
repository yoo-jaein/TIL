# Primitive type vs Reference type
자바 데이터 타입 중 기본형과 참조형

## 자바의 데이터 타입
자바의 자료형은 Primitive type(기본형)과 Reference type(참조형)으로 나뉜다. 

## Primitive type
자바의 기본형에는 8가지 타입이 있다.
- boolean
- byte
- short
- char
- int
- long
- float
- double

## Reference type
기본형을 제외한 모든 나머지 타입은 참조형이다. 
- String
- Thread
- class
- int[]
- char[]
- enum
- ...

## 기본형과 참조형의 차이
### 1. 용도
첫 번째, 기본형 변수는 값을 저장하기 위한 것이고 참조형 변수는 힙 메모리 영역에서 생성되는 객체를 다루기 위한 것이다. 여기서 기억해야 할 것은, 참조형은 포인터가 아니며 객체에 접근하여 상태를 변경하는 용도라는 것이다.  

### 2. null
두 번째, 기본형은 null이 아닌 값을 가지지만 참조형은 값의 부재를 가리키는 null 값을 가질 수 있다. 예를 들어, 기본형 int 변수를 초기화 하지 않은 상태로 두면 그 변수의 기본 값은 0이다. 반면, 참조형 Integer 변수의 기본 값은 null이다. 그래서 초기화하지 않은 참조형 변수에 접근하면 NullPointerException이 발생하게 된다. 이런 오류를 피하기 위해 기본형과 참조형의 차이를 잘 이해하는 것이 중요하다.  

#### 기본형의 기본 값(default)
- boolean: false
- byte, short, char, int, long: 0
- float, double: 0.0

### 3. 복사
세 번째, 복사(=)에서 차이가 있다. 코드를 살펴보자.

```java
int a = 100;
int b = a;

a++; 

// a == 101, b == 100
```

기본형 변수는 단순히 값을 할당한다. 

```java
List<String> original = new ArrayList(2);
List<String> copy = original;

copy.add("First");
copy.add("Second");

// original == {"First", "Second"}, copy == {"First", "Second"};
```

반면, 참조형 변수는 참조를 할당한다. copy에 original의 참조를 복사했기 때문에 original과 copy는 동일한 데이터를 가리킨다. 그래서 copy에만 원소를 추가했음에도 불구하고 original에도 동일한 변화가 일어나게 된다.

### 4. 비교
네 번째, 비교(==)에서 차이가 있다. 마찬가지로 코드를 살펴보자.

```java
int a = 100;
int b = 100;

if (a == b)
    System.out.println("a와 b는 동일하다."); // 출력된다.
```

기본형 변수끼리 비교(==)할 때 가지는 값이 같으면 동일한 것으로 간주된다.

```java
String color = new String("red");
String colour = new String("red");

if (color == colour)
    System.out.println(""); // 출력되지 않는다.

if (color.equals(colour))
    System.out.println("color와 colour는 동일하다.") // 출력된다.
```

반면, 참조형 변수끼리 비교할 때 (==)을 사용한다면, 가지는 값이 같아도 각각 다른 곳을 참조하고 있기 때문에 다른 것으로 간주된다. 만약, equals() 메서드로 비교한다면 동일한 것으로 간주된다. 따라서 참조형 변수끼리 비교할 때는 항상 equals() 메서드를 사용해야 한다.

## 참고
https://stackoverflow.com/questions/8790809/whats-the-difference-between-primitive-and-reference-types  
https://javarevisited.blogspot.com/2015/09/difference-between-primitive-and-reference-variable-java.html#axzz738dV2tQM  