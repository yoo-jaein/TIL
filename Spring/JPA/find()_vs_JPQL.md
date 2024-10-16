# find() vs JPQL
em.find()와 JPQL의 내부 동작 방식의 차이

## find()
```java
Member member1 = em.find(Member.class, 1L); //최초 조회, 데이터베이스에서 가져옴

Member member2 = em.find(Member.class, 1L); //두 번째 조회, 영속성 컨텍스트에서 가져옴

//member1 == member2 성립
```

em.find() 메서드는 엔티티를 영속성 컨텍스트에서 먼저 찾고 없으면 데이터베이스에서 찾는다.  

0. 영속성 컨텍스트는 비어있는 상태.
1. 위 코드에서 첫 번째 find()를 호출하면 데이터베이스에서 id가 1L인 회원 엔티티를 조회하고 영속성 컨텍스트에 등록한다.
2. 두 번째 find()를 호출하면 먼저 영속성 컨텍스트에서 id가 1L인 회원 엔티티를 찾는다. 만약 없다면 데이터베이스에서 찾는다.
3. 영속성 컨텍스트에 id가 1L인 회원 엔티티가 이미 있으므로 영속성 컨텍스트(1차 캐시)에 있는 엔티티를 반환한다.

해당 엔티티가 영속성 컨텍스트에 있으면 메모리(1차 캐시)에서 바로 찾기 때문에 성능상의 이점이 있다.  

## JPQL
```java
//첫 번째 호출, 데이터베이스 조회 발생
Member member2 =
    em.createQuery("select m from Member m where m.id = :id", Member.class)
    .setParameter("id", 1L)
    .getSingleResult();

//두 번째 호출, 데이터베이스 조회 발생
Member member2 =
    em.createQuery("select m from Member m where m.id = :id", Member.class)
    .setParameter("id", 1L)
    .getSingleResult();

//member1 == member2 성립
```
find()와 마찬가지로 주소 값이 같은 인스턴스를 반환한다. 하지만 내부 동작 방식은 조금 다르다. JPQL은 항상 데이터베이스에 SQL을 실행해서 결과를 조회한다.  

0. 영속성 컨텍스트는 비어있는 상태.
1. 위 코드에서 첫 번째 JPQL을 호출하면 데이터베이스에서 id가 1L인 회원 엔티티를 조회하고 영속성 컨텍스트에 등록한다.  
2. 두 번째 JPQL을 호출하면 데이터베이스에서 id가 1L인 회원 엔티티를 조회한다.
3. 영속성 컨텍스트에 id가 1L인 회원 엔티티가 이미 있으므로 2번에서 조회한 엔티티는 버리고 기존 엔티티를 반환한다.

## 왜 데이터베이스에서 새로 조회한 엔티티를 버릴까?
https://cheese10yun.github.io/jpa-jpql/#jpql-2  
트랜잭션 격리 수준 때문

## 결론
- find()는 1차 캐시의 성능상 이점을 가질 수 있다. 
- JPQL은 항상 데이터베이스에 SQL을 실행하여 결과를 조회한다.
- 영속성 컨텍스트는 영속 상태인 엔티티의 동일성을 항상 보장한다.

## 참고
자바 ORM 표준 JPA 프로그래밍 10장  
