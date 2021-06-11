# Querydsl Fetchable<T\>
Querydsl의 Fetchable<T\> 인터페이스는 쿼리 구현을 위한 기본적인 프로젝션 메서드들을 제공한다.  

## fetch()
결과를 List<T\>로 반환한다. 결과가 없는 경우 빈 List를 반환한다. 조회 대상이 여러 건일 경우 사용한다. 

```java
List<Member> members = queryFactory
  .selectFrom(member)
  .where(member.username.eq(username))
  .fetch();
```

## fetchFirst()
결과를 하나 반환하거나 결과가 없는 경우 null을 반환한다. 내부적으로 limit(1).fetchOne()으로 구현되어 있기 때문에 결과가 두 개 이상인 경우에는 첫번째 결과를 반환한다.  

```java
Member findMember = queryFactory
  .selectFrom(member)
  .fetchFirst();
```

## fetchOne()
결과를 unique한 하나로 반환하거나 결과가 없는 경우 null을 반환한다. 만약 매칭된 결과가 두 개 이상인 경우 NonUniqueResultException을 발생시키기 때문에, 쿼리 결과가 unique한 것이 확실할 때 사용해야 한다.  


```java
Member findMember = queryFactory
  .selectFrom(member)
  .fetchOne();
```

## iterate()
결과를 com.mysema.commons.lang.CloseableIterator<T\>로 반환한다. 대량의 데이터에 대한 쿼리를 해야하는 경우 사용한다. 대량의 레코드가 동시에 메모리에 올라가게 되면 메모리 누수 문제가 발생할 수 있다. iterate()의 결과는 스크롤이 가능한 형태로 반환되어 모든 데이터가 동시에 메모리에 올라가는 것을 막아준다. 자주 사용되는 메서드는 아니다.  

```java
  CloseableIterator<Tuple> results = query().from(survey).select(survey.id, survey.name).iterate();
  assertTrue(results.hasNext());
  
  while (results.hasNext()) { 
    assertEquals(2, results.next().size());
  } 
  
  results.close();
```

## fetchResults()
결과를 QueryResults<T\>로 반환한다. QueryResults에는 결과 List<T\>와 페이징을 위한 정보들이 들어있다. 그 중에 total count 정보가 포함되기 때문에 카운트를 위한 쿼리가 추가로 실행된다. 결과 List<T\>를 꺼내야 하는 경우 getResults()를 사용한다.  

```java
QueryResults<Member> results = queryFactory
  .selectFrom(member)
  .fetchResults();
```

## fetchCount()
결과의 개수를 long으로 반환한다.

```java
long count = queryFactory
  .selectFrom(member)
  .fetchCount();
```

## 참고
http://www.querydsl.com/static/querydsl/4.2.1/apidocs/  
http://www.querydsl.com/static/querydsl/4.2.1/apidocs/com/querydsl/core/QueryResults.html  
https://stackoverflow.com/questions/62261417/can-querydsl-handle-bigger-amounts-of-data  
https://github.com/querydsl/querydsl/issues/2237  
