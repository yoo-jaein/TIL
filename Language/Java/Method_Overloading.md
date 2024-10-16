# Method Overloading
한 클래스 내에서 같은 이름의 메서드를 여러 개 정의하는 것을 메서드 오버로딩이라 한다.  

## 메서드 시그니처
```java
int add(int a, int b);
```

메서드 시그니처란 메서드의 선언부에 명시되는 메서드의 이름과 매개 변수의 리스트를 가리킨다. 자바 컴파일러는 이 메서드 시그니처를 통해 오버로딩된 메서드들을 구별하고 적절하게 실행시킨다.   

## 오버로딩의 조건
메서드 오버로딩의 조건은 다음과 같다.  

1. 메서드 이름이 같아야 한다.  
2. 메서드 시그니처(매개 변수의 개수 또는 타입)이 달라야 한다.  

## 예시
```java
void println();
void println(boolean x);
void println(char x);
void println(int x);
void println(double x);
void println(float x);
void println(Object x);
void println(String x);
...
```
println() 메서드를 호출할 때 매개 변수로 넘겨주는 값의 타입에 따라 오버로딩된 메서드들 중 하나가 선택되어 실행된다.

## 장점
1. 동일한 기능을 하는 메서드들이 하나의 이름을 가지게 되면서 코드 가독성이 높아진다.
2. 메서드의 이름을 절약할 수 있다.

## 주의!
- 오버로딩을 너무 많이 만들면 코드만 보고 어떤 메서드가 호출되었는지 이해하기 어렵다. 특히 오버로드된 매개 변수 타입이 다른 매개 변수로부터 상속받았을 때 발생하는 문제이다.
- 오버로딩은 리턴 타입과는 관계가 없다. 매개 변수는 같고 리턴 타입이 다른 경우는 오버로딩이 성립되지 않는다.

## 참고
https://ko.wikipedia.org/wiki/%ED%95%A8%EC%88%98_%EC%98%A4%EB%B2%84%EB%A1%9C%EB%93%9C  
https://programmer-seva.tistory.com/78  