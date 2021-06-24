# JPQL
JPA를 사용하면 개발자는 엔티티 객체를 중심을 개발하고 데이터베이스에 대한 처리는 JPA에 맡긴다. 문제는 검색 쿼리다. JPA는 엔티티 객체를 중심으로 개발하므로 검색을 할 때도 데이터베이스 테이블이 아닌 엔티티 객체를 대상으로 검색해야 한다. 그런데 엔티티 객체를 대상으로 검색하려면 데이터베이스의 모든 데이터를 애플리케이션으로 불러와서 엔티티 객체로 변경한 다음 검색해야 하는데, 이는 사실상 불가능하다. 애플리케이션에 필요한 데이터만 불러오려면 검색 조건이 포함된 SQL을 사용해야 한다. JPA는 JPQL로 이런 문제를 해결한다.  

## SQL과 JPQL의 차이
SQL은 데이터베이스 테이블을 대상으로 쿼리한다.  
JPQL은 엔티티 객체를 대상으로 쿼리한다.  

## JPQL 문법
JPQL은 SQL과 문법이 거의 유사해서 select, from, where, group by, having, join 등을 사용할 수 있다.  

```java
String jpql = "select m.id, m.name, m.age from Member m";
```

여기서 from Member는 회원 엔티티 객체를 말하는 것이지 MEMBER 테이블이 아니다. JPQL은 데이터베이스 테이블을 전혀 알지 못한다.

## JPA에서 JPQL을 사용하는 방법
1. em.createQuery(JPQL, 반환 타입) 메서드를 실행해서 쿼리 객체를 생성한 후,
2. 쿼리 객체의 getResultList() 메서드를 호출한다.

JPA는 JPQL을 분석해서 적절한 SQL을 만들고 데이터베이스에서 데이터를 조회한다.

## 참고
자바 ORM 표준 JPA 프로그래밍 2장  