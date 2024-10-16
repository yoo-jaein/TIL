# Query method or JPQL or Querydsl
Repository를 개발할 때 스프링 데이터 JPA의 Query method 또는 JPQL 또는 Querydsl 코드를 사용할 수 있다. 그렇다면 이 중 어떤 방식을 사용하는 것이 가장 효과적일까?

## Spring Data JPA Query method
스프링 데이터 JPA의 쿼리 메서드는 JPA repository interface에 메서드명만 정의하면 코드를 자동으로 생성해주는 기능이다. https://docs.spring.io/spring-data/commons/docs/2.5.0/reference/html/#appendix.query.method.subject 에서 지원되는 쿼리 메서드 키워드를 확인할 수 있다.  

```java
List<Member> findByUsernameAndAgeGreaterThan(String username, int age);
```

이 방법은 쿼리 조건이 많아지면 많아질수록 코드 가독성이 떨어지는 문제가 있다. 따라서 단순한 쿼리까지는 쿼리 메서드로 정의하고, 조건이 복잡해지면 JPQL이나 Querydsl 중 하나를 사용하는 방식을 생각해볼 수 있다.  

하지만 쿼리 메서드의 더 큰 단점은 무엇보다 비즈니스 로직의 컨텍스트를 메서드명으로 표현하지 못한다는 것이다. 활동하지 않는 휴면 회원의 정의를 마지막 로그인, 회원 등급, 회원 상태로 정의하는 경우 이것을 쿼리 메서드로 작성하면 다음과 같이 작성해야 한다.  

```java
List<Member> findByLastLoginatAndStatusAndGrade(LocalDateTime lastLoginAt, int status, int grade);
```

이것은 그냥 쿼리조건문을 뜻할 뿐 활동하지 않는 휴면 회원을 뜻하지 않는다. 유추는 할 수 있으나 의미를 정확하게 전달하는 것은 아니다. 차라리 Querydsl로 코드를 작성하고 findDormancyMember 이름을 붙이는 것이 좋다고 생각한다.  

또한, 쿼리 메서드는 인터페이스 기반으로 작성하기 때문에 테스트 코드를 작성하지 않는 경우도 많다. 

## JPQL
JPQL은 SQL과 문법이 유사한 객체 지향 쿼리이다. EntityManager의 createQuery() 또는 @Query 어노테이션을 통해 JPQL을 직접 작성할 수 있다.   

```java
String qlString = 
    "selece m from Member m " +
    "where m.username = :username";

Member findMember = em.createQuery(qlString, Member.class)
    .setParameter("username", "member1")
    .getSingleResult();
```

```java
@Query("select m from Member m where m.username = :username and m.age = :age")
List<Member> findUser(@Param("username") String username, @Param("age") int age);
```

스프링 데이터 JPA의 @Query 어노테이션을 사용하면 JPA Named 쿼리처럼 애플리케이션 실행 시점에 문법 오류를 발견할 수 있다. 그러나 파라미터 바인딩(위치 기반/이름 기반)을 직접 해줘야하는 단점이 있다.

## Querydsl
Querydsl은 JPQL 빌더이다. 쿼리를 메서드로 구조화하여 자바 코드 기반으로 작성할 수 있게 도와주는 라이브러리이다. 즉, JPQL의 사용성을 높이기 위해 확장된 라이브러리가 Querydsl인 것이다. 따라서 대부분의 경우 고민없이 Querydsl로 가면 된다. 

```java
return queryFactory
        .select(new QMemberTeamDto(
            member.id,
            member.username,
            member.age,
            team.id,
            team.name
        ))
        .from(member)
        .leftJoin(member.team, team)
        .where(
            usernameEq(condition.getUsername()),
            teamNameEq(condition.getTeamName()),
            ageGoe(condition.getAgeGoe()),
            ageLoe(condition.getAgeLoe())
        )
        .fetch();
```

QClass, 자바 코드 기반으로 작성하기 때문에 IDE의 자동 완성 기능을 적극 활용할 수 있으며 오류를 잡기 좋은 것이 장점이다. 또한, 코드 가독성도 앞서 설명한 두 방법에 비해 뛰어나므로 Querydsl을 사용하는 것이 가장 효과적이라고 생각한다. 

## 결론
1. findByEmail()와 같이 직관적인 쿼리만 쿼리 메서드로 만들고 나머지는 Querydsl을 사용하거나,  
2. 예외 없이 모든 케이스에 대해 Querydsl을 사용하는 것이 좋다. (추천)  

## 참고
https://www.inflearn.com/questions/38771  
https://dico.me/java/articles/290/ko  
https://www.popit.kr/jpa-%EC%84%A0%ED%98%B8%ED%95%98%EB%8A%94-%ED%8C%A8%ED%84%B4/