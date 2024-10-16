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

## 아이디어

1. 엔티티를 생성하는게 너무 복잡하면 서비스 단에서 문제를 해결하지 말고, 별도의 생성 클래스를 만들어 문제를 해결해보자.  
    생성 클래스가 필요한 DTO와 Repository를 모두 의존하고 최종 완성된 엔티티를 반환하는 형식으로 사용한다. 생성이 한 곳에 집중되는 장점이 있다.
   

2. 엔티티 생성 로직이 단순할 때는 생성자를 쓰고 복잡해지면 빌더나 팩토리 오브젝트를 사용하자.


3. 절대로 엔티티가 View용 DTO를 의존하면 안 된다. 반대로 View용 DTO가 엔티티를 의존하는 것은 괜찮다.


4. DTO를 Repository에서 생성하면 해당 Repository와 같은 패키지에 있어야 패키지 의존관계가 안전하게 유지된다.  
    DTO의 위치는 의존관계와 패키지 의존관계를 고민해봐야 한다. 순환 참조를 조심해야 한다.  

## 참고
https://www.inflearn.com/questions/19419  
https://www.inflearn.com/questions/30076  
https://www.inflearn.com/questions/139564  
https://dzone.com/articles/dtos  