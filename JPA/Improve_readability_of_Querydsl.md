# Improve readability of Querydsl
Querydsl 코드의 가독성을 높이는 방법  

## 방법 1 : select()와 from() 합치기
쿼리에서 select()의 대상과 from()의 대상이 같은 경우 selectFrom()으로 합친다.

## 방법 2 : where()의 and 연결을 파라미터로 만들기
where() 안의 조건이 and로 연결되어 있는 경우 ','으로 분리하여 파라미터로 만든다. where()은 파라미터가 여러 개일 때 내부적으로 and 처리를 해준다.  

## 방법 3 : 쿼리 결과를 바로 리턴하기
추가 작업이 없다면 쿼리 결과를 변수에 담지 않고 바로 return 한다.

## 방법 4 : QuerydslRepositorySupport 상속 제거하기
JPAQueryFactory를 얻기 위해 QuerydslRepositorySupport를 상속하는 경우가 있다. 하지만 이 상속 관계 때문에 Repository마다 도메인 클래스와 JPAQueryFactory를 전달하는 코드가 필요하다. 따라서 QuerydslRepositorySupport가 반드시 필요한 경우가 아니라면 상속을 제거한다.  

## 방법 5 : 동적 쿼리에 BooleanExpression 사용하기
동적 쿼리를 만들 때 BooleanBuilder와 if문으로 구성하는 경우 BooleanExpression을 반환하는 메서드로 바꾼다. where절 안에서 파라미터로 연결이 가능하여 매끄럽게 동적 쿼리를 작성할 수 있다. 만약 where()의 조건이 null이라면 테이블 전체를 탐색하기 때문에 null 처리가 필요하다.   

## 방법 6 : static import하기
Q클래스나 Expressions.asString을 static import하여 코드 길이를 줄인다.

## 방법 7 : Entity, DTO 분리하기
쿼리의 프로젝션 대상이 Entity인 경우 DTO로 분리한다.

## 방법 8 : 상속, 구현 없는 Repository로 만들기
방법 4를 더 확장시킨 방법이다. 최소한의 Bean 등록을 위해 @Repository를 선언하고, JPAQueryFactory를 선언한다. 특정 Entity를 메인으로 확정할 수 없는 경우 혹은 자주 변경되는 경우에 적용할 수 있다.

### 참고
https://jojoldu.tistory.com/372
