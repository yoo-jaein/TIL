# N+1 Query problem in JPA
N+1 문제의 발생 원인과 해결 방법

## N+1 문제

JPA를 사용하면서 가장 조심해야 하는 것이 바로 N+1 문제다. 엔티티를 조회할 때 연관된 엔티티를 로딩하는 전략이 즉시 로딩이면 데이터베이스에 JOIN SQL 쿼리를 사용해서 한 번에 연관된 엔티티까지 조회한다. 문제는 JPQL을 사용할 때 발생한다. JPA가 JPQL을 분석해서 SQL을 생성할 때는 글로벌 페치 전략을 참고하지 않고 오직 JPQL 자체만 사용한다. 즉시 로딩이든 지연 로딩이든 구분하지 않고 JPQL 쿼리 자체에 충실하게 SQL을 만든다. 그럼 다음의 과정을 통해 SQL 문이 생성된다.

1. select o from Order o JPQL을 분석해서 select * from Order SQL을 생성한다.
2. 데이터베이스에서 결과를 받아 order 엔티티 인스턴스들을 생성한다.
3. (글로벌 페치 전략이 즉시 로딩인 경우) Order.member에서 order를 로딩하는 즉시 연관된 member도 로딩한다.
   1. (글로벌 페치 전략이 지연 로딩인 경우) Order.member가 사용될 때 member를 로딩하며 다음 과정들이 진행된다.
4. 연관된 member를 영속성 컨텍스트에서 찾는다.
5. 만약 영속성 컨텍스트에 없으면 select * from member where id=? SQL을 조회한 Order 엔티티의 수만큼 실행한다.

만약 조회한 order 엔티티가 10개면 member를 조회하는 SQL도 10번 실행한다. 이처럼 처음 조회한 데이터 수만큼 다시 SQL을 사용해서 조죄하는 것을 N+1 문제라 한다. N+1이 발생하면 SQL이 상당히 많이 호출되므로 조회 성능에 치명적이다. 따라서 최우선적으로 최적화할 대상이다.

## N+1 문제 해결하기
N+1 문제는 JPQL 페치 조인(fetch JOIN)으로 해결할 수 있다.  

### JPQL 페치 조인
페치 조인을 하면 JPQL을 호출하는 시점에 함께 로딩할 엔티티를 선택할 수 있다. 

#### 페치 조인 사용 전
```sql
JPQL: select o from Order o
SQL: select * from Order
```

#### 페치 조인 사용 후
```sql
JPQL: select o from Order o join fetch o.member
SQL: select o.*, m.* from Order o join Member m on o.MEMBER_ID=m.MEMBER_ID
```

페치 조인은 조인 명령어 마지막에 fetch를 넣어주면 된다. 페치 조인을 사용하면 SQL JOIN을 사용해서 페치 조인의 대상까지 함께 조회한다. 따라서 N+1 문제가 발생하지 않는다.
