# Arrays.asList() 사용 시 UnsupportedOperationException 발생
```java
Page page1 = new Page("1페이지");
Page page2 = new Page("2페이지");

List<Page> note = Arrays.asList(page1, page2);
```
note 객체에 데이터를 넣어주기 위해 Arrays.asList()를 사용했다. 이처럼 Arrays.asList()를 사용하는 경우 주의해야 할 점이 있다.

## UnsupportedOperationException
```java
Page page1 = new Page("1페이지");
Page page2 = new Page("2페이지");

List<Page> note = Arrays.asList(page1, page2);

note.clear();
```

```java
Page page1 = new Page("1페이지");
Page page2 = new Page("2페이지");

List<Page> note = Arrays.asList(page1, page2);

Page page3 = new Page("3페이지");
note.add(page3);
```
위의 코드에서 모두 UnsupportedOperationException이 발생한다. 그 이유는 Arrays.asList()로 생성한 리스트와 ArrayList가 내부 구현에서 차이가 있기 때문이다.  

### ArrayList
java.util.**ArrayList**에 구현되어 있다. java.util.ArrayList에는 add, remove, clear 등의 메서드들이 구현되어 있다.  

### Arrays.asList()
Arrays.asList()는 java.util.**Arrays.ArrayList**의 new ArrayList<>()를 리턴한다. 고정된 사이즈(fixed-size)의 리스트를 리턴하기 때문에 Arrays.asList()로 만든 리스트에 변경이 일어나는 경우 예외가 발생한다.  

## 결론
```java
List<Page> note = new ArrayList<>(Arrays.asList(page1, page2));
```
리스트에 원소의 추가, 삭제 등 변경이 일어날 가능성이 있는 경우 반드시 ArrayList로 생성해야 한다.

## 참고
https://stackoverflow.com/questions/43020075/java-util-arrays-aslist-when-used-with-removeif-throws-unsupportedoperationexcep  