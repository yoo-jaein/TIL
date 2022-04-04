# JPA FetchType
객체는 객체 그래프로 연관된 객체들을 탐색하지만, 데이터베이스는 실행하는 SQL에 따라 어디까지 탐색할 수 있는지가 정해진다. JPA는 이 불일치 문제를 해결하기 위해 프록시 기술로 연관된 엔티티를 조회하는 시점을 조절할 수 있다. 연관된 엔티티를 조회하는 시점에는 즉시 로딩과 지연 로딩이 있다.

## 즉시 로딩
- 즉시 로딩(Eager Loading)이란 엔티티를 조회할 때 연관된 엔티티도 함께 조회하는 것이다.
- @ManyToOne(fetch = FetchType.EAGER)로 설정한다.
- Hibernate는 @JoinColumn(nullable = true) 외래 키에 null 값을 허용하는 경우 (기본 값) LEFT OUTER JOIN을 하고, null 값을 허용하지 않는 경우 INNER JOIN을 사용한다.

## 지연 로딩
- 지연 로딩(Lazy Loading)이란 연관된 엔티티를 실제 사용할 때 조회하는 것이다.
- @ManyToOne(fetch = FetchType.LAZY)로 설정한다.
- JPA는 지연 로딩을 구현하기 위해 프록시 기술을 사용한다.
  - em.find(Member.class, "member1"); 은 데이터베이스를 바로 조회한다.
  - em.getReference(Member.class, "member1"); 은 데이터베이스 접근을 위임한 프록시 객체를 반환한다. (데이터베이스 조회 X, 엔티티 객체 생성 X)
  - 프록시 클래스는 실제 클래스를 상속 받아서 만들어지고, 그 클래스와 겉모습이 같다.
  - 프록시 객체는 실제 객체에 대한 참조를 보관하고 있어서 프록시 객체의 메서드가 호출되면 실제 객체의 메서드를 반환해준다.
  - 프록시 객체는 member.getName()처럼 실제 사용될 때 데이터베이스를 조회해서 엔티티 객체를 생성하는데, 이를 프록시 객체의 초기화라고 한다.
  - 참고로 만약 영속성 컨텍스트에 찾는 엔티티가 이미 있다면 getReference()는 프록시가 아닌 실제 엔티티를 반환한다.

## 참고
자바 ORM 표준 JPA 프로그래밍 8장  