# QuerydslRepositorySupport
Querydsl 라이브러리를 이용하여 Repository를 구현하기 위한 클래스  

## 장점
Custom 메서드를 작성할 때 상속을 통해 from(), applyPagination() 등의 서포트 기능을 사용할 수 있다.

## 제약조건
QuerydslRepositorySupport를 상속하게 되면 어떤 타입의 Entity를 도메인으로 사용하는지 super에 반드시 전달해줘야 한다. 또한, select로 시작하는 일반적인 쿼리와 다르게 from으로 시작되어야하는 제약 조건이 있다.

## 한계
Querydsl 3.x 버전을 대상으로 만들어졌기 때문에 4.x 버전의 JPAQueryFactory로 쿼리를 구성할 수 없다. 즉, select()로 시작이 불가능하다. 그리고 Spring Data의 Sort 기능도 사용 불가능하다.

이러한 한계를 극복하기 위해 직접 Support 클래스를 만들 수 있다.

## 참고
https://docs.spring.io/spring-data/data-jpa/docs/current/api/org/springframework/data/jpa/repository/support/QuerydslRepositorySupport.html  
