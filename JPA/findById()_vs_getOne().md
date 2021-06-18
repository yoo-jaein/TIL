# findById() vs getOne()
Spring Data JPA에서 단건 조회를 할 때 findById() 혹은 getOne()을 사용할 수 있다. 둘의 차이는 무엇일까?

## find-
- findById() 메서드는 CrudRepository 인터페이스에 들어있다. 내부적으로 EntityManager의 find() 메서드를 사용한다.
- null이 예상되는 상황에서 '그냥' 찾아보는 것이다. 결과에 엔티티가 반환될 수도 있고 null이 반환될 수도 있다. 따라서 Optional<>을 반환하는 메서드의 경우 find 명칭을 사용한다.

## get-
- getOne() 메서드는 JpaRepository 인터페이스에 들어있다. 내부적으로 EntityManager의 getReference() 메서드를 사용한다. 
- 반드시 하나를 가져올 수 있는 상황에서 찾아보는 것이다. 워크플로우에서 엔티티의 존재가 확실한 상황에, 단일 결과를 반환하는 메서드의 경우 get 명칭을 사용한다. 만약 해당되는 결과가 없다면 exception으로 간주한다. 하지만 이 exception은 persistence layer에서 반드시 핸들링할 필요는 없다.

## 예시
- find는 서점에 가서 책 이름으로 책을 검색하는 것이다. 검색된 명칭에 해당하는 책이 존재하지 않을 수도 있다.  
- get은 주문된 책의 가격을 알아내기 위해 주문 데이터베이스에서 책을 찾는 것이다. 그 책은 데이터베이스에 반드시 존재해야 하며, 그렇지 않으면 어딘가 문제가 있는 것이다.  

## 결론
- find와 get 구분은 엄격한 규칙은 아니고 semantic rule일 뿐이다. 만약 프로젝트에서 find와 get의 의미를 명확히 하고 사용한다면 워크플로우를 이해하는데 도움이 될 순 있다.  
- Optional을 반환하는 메서드 이름에는 find를 사용하고, 단건 조회를 하는 메서드 이름에는 get을 사용한다. List, Set, Page의 경우 find를 많이 사용한다.  

## 참고
https://tuhrig.de/find-vs-get/  
https://szymonkrajewski.pl/the-practical-difference-between-findby-and-getby-in-repositories/