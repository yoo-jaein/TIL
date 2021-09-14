# Collections.singletonList() vs List.asList()
```java
Arrays.asList(item.getMember());
```
위와 같이 IntelliJ에서 하나의 원소를 가진 배열을 Arrays.asList로 작성하면 Call to 'asList() with only one argument'라는 메시지를 볼 수 있다. 이 말은 즉슨 하나의 원소를 가진 배열을 사용할 때는 Collections.singletonList()를 사용하라는 말이다. 배열의 표현하는데 있어서 Collections.singletonList()와 List.asList() 중 어떤 것을 사용하는 것이 베스트일까?

## Collections.singletonList()
- 사이즈가 1로 고정된다.
- 값을 변경할 수 없다.
- 값이나 구조를 바꾸면 UnsupportedOperationException이 발생한다.

처음에 소개한 코드와 같이 하나의 요소만 가진 배열의 경우 Collections.singletonList()를 사용하는 것이 권장된다.

## List.asList()
- 배열의 고정된 사이즈를 가진다.
- 값을 변경할 수 있다.
- 구조를 바꾸면 UnsupportedOperationException이 발생한다.

## 참고
https://dzone.com/articles/singleton-list-showdown-collectionssingletonlist-v  
https://stackoverflow.com/questions/26027396/arrays-aslist-vs-collections-singletonlist  
