# @Modifying
Spring Data JPA에서 @Query 어노테이션과 함께 사용되는 어노테이션

## 적용
UPDATE, DELETE 쿼리일 때 @Modifying 어노테이션을 붙이며, SELECT 쿼리에는 사용하지 않는다. 만약 벌크성 수정, 삭제 쿼리에 @Modifying 어노테이션을 붙이지 않으면 org.hibernate.hql.internal.QueryExecutionRequestException이 발생한다.

## 기능
```java
@Modifying
@Query("update Member m set m.age = m.age + 1 where m.age >= :age")
int bulkAgePlus(@Param("age") int age);
```
위의 코드는 스프링 데이터 JPA를 사용한 벌크성 수정 쿼리이다. 이러한 벌크성 쿼리는 영속성 컨텍스트를 무시하고 DB에 바로 실행된다. 따라서 쿼리 실행 이후 영속성 컨텍스트에 있는 엔티티의 상태와 DB의 엔티티의 상태가 달라질 수 있다.  


@Modifying을 붙이면 EntityManager가 쿼리를 실행한 후 영속성 컨텍스트를 clear한다. 이렇게 하면 영속성 컨텍스트 안의 outdated entities를 효과적으로 제거할 수 있다.    


만약 clear를 수행하고 싶지 않다면 @Modifying의 clearAutomatically 속성을 false로 변경하면 된다.  

## 수정, 삭제 쿼리가 나가는 모든 메서드에 붙여줘야 할까?
custom 구현 메서드 또는 메서드 이름에서 파생되는 쿼리(Spring Data JPA Query method)에는 적용할 필요가 없다. 이미 기본적인 데이터 액세스 권한을 갖거나 이름으로 수정의 여부를 감지하기 때문에 @Modifying을 사용하지 않아도 된다. 

## 참고
https://docs.spring.io/spring-data/jpa/docs/current/api/org/springframework/data/jpa/repository/Modifying.html  
https://stackoverflow.com/questions/43665090/why-do-we-have-to-use-modifying-annotation-for-queries-in-data-jpa  
