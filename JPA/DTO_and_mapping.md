# DTO and mapping
엔티티에서 DTO로 변환하거나, DTO에서 엔티티로 변환하고 싶을 때 어떤 방식으로 접근해야 할까?

## Entity-DTO mapping
엔티티와 DTO를 서로 변환시켜주는 작업을 매핑이라 부른다. 매핑에는 여러 방법이 있다.
1. dedicated mapper class
2. 생성자 (추천)
3. declarative mapping (Dozer)

## 매핑은 서비스 단에서
컨트롤러에서 매핑하지 말고 서비스 단에서 매핑해야 한다. 컨트롤러 단에서 매핑하면 다음과 같은 문제가 발생한다.  

```java
XXDto found = XXService.findById(id)
XX foundToEntity = XXDto.toEntity()
```

이 코드는 컨트롤러에서 XXService도 의존하고, XXDto도 의존하고, XXEntity도 의존한다. 컨트롤러가 모든 곳에 의존되는 상황이다. 차라리 XXEntity entity = XXService.findById(id)라고 하면 컨트롤러의 의존관계 부담을 줄일 수 있다. 혹은 컨트롤러가 엔티티에 의존하지 않도록 만들려면 컨트롤러가 XXService, XXDto에만 의존하도록 구성해야 한다. 

## 참고
https://www.inflearn.com/questions/139564  
https://dzone.com/articles/dtos  