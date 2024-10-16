# JPA Entity Mapping
JPA 엔티티 매핑하기

## @Entity
- 테이블과 매핑할 클래스에 붙인다.

## @Table
- 엔티티와 매핑할 클래스를 명시한다. 
- 생략 가능하다. 생략할 경우 @Entity가 붙은 클래스는 클래스 이름의 테이블에 매핑된다.
- uniqueConstraints와 같은 테이블 제약 조건은 DDL 자동 생성에만 사용되고 JPA나 애플리케이션의 로직과는 무관하다.

## @Id
- 기본 키를 매핑한다.
- JPA는 @Id 값으로 엔티티를 식별하기 때문에 필수다.
- null을 허용하지 않고 유니크하며 변경의 여지가 적은 값을 기본 키로 결정해야 한다.
- 기본 키를 애플리케이션에서 직접 할당할 경우 em.persist()로 엔티티를 저장하기 전에 setter로 기본 키를 세팅한다.
- 기본 키를 데이터베이스에 위임하려는 경우 IDENTITY, SEQUENCE, TABLE 세 가지 전략 중 하나를 선택한다.

## @GeneratedValue(strategy = GenerationType.IDENTITY)
- MySQL의 auto_increment 기능처럼 데이터베이스에 엔티티를 저장해야 기본 키 값을 받아올 수 있을 때 사용하는 전략이다.
- 이 전략을 사용하면 JPA는 기본 키 값을 받아오기 위해 데이터베이스를 추가로 조회해야 한다. 
- 만약 JPA 구현체로 Hibernate를 사용하면 내부적으로 Statement.getGeneratedKeys()를 사용해서 데이터베이스에 데이터를 저장하고 생성된 기본 키 값도 얻어오기 때문에 데이터베이스와 1번만 통신한다.
- 이 전략에서 트랜잭션을 지원하는 쓰기 지연이 동작하지 않는다. 엔티티를 데이터베이스에 저장해야만 기본 키 값을 구할 수 있기 때문에 em.persist()를 호출하는 즉시 INSERT SQL이 데이터베이스에 전달된다.

## @Column
- 컬럼을 매핑한다.
- length, nullable과 같은 컬럼 제약 조건은 DDL 자동 생성에만 사용되고 JPA나 애플리케이션의 로직과는 무관하다.

## @Enumerated
- 자바의 enum을 매핑한다.
- 기본 값은 @Enumerated(EnumType.ORDINAL)로 enum에 정의된 순서(1, 2, 3...)대로 저장된다.
- @Enumerated(EnumType.STRING)으로 enum의 이름(ADMIN, USER..)을 저장할 것을 권장한다.

## @Temporal
- 자바의 Date, Calander를 매핑한다.

## @Lob
- CLOB, BLOB을 매핑한다.

## @Transient
- 데이터베이스에 매핑하지 않는 필드다.
- 객체에 어떤 값을 임시 보관할 때 사용한다.

## @Access
- 엔티티의 필드에 접근하는 방식을 결정한다.
- AccessType.FIELD는 엔티티의 필드에 직접 접근한다. 
- AccessType.PROPERTY는 엔티티의 getter 메서드로 접근한다.

## 참고
자바 ORM 표준 JPA 프로그래밍 4장