# Container vs Application managed EntityManager
Container-managed EntityManger vs Application-managed EntityManager

## Container-managed
컨테이너가 관리하는 엔티티 매니저  

순수 자바 환경에서는 엔티티 매니저 팩토리에서 엔티티 매니저를 직접 생성하여 사용했다. 스프링이나 J2EE 컨테이너를 사용하면 컨테이너가 엔티티 매니저를 관리하고 제공해준다. 따라서 엔티티 매니저 팩토리에서 엔티티 매니저를 직접 생성해서 사용하는 것이 아니라 컨테이너가 제공하는 엔티티 매니저를 사용하면 된다.   

### @PersistenceContext
```java
@PersistenceContext //엔티티 매니저 주입
private EntityManager entityManager;
```

@PersistenceContext는 컨테이너가 관리하는 엔티티 매니저를 주입하는 어노테이션이다. 컨테이너의 큰 장점은 트랜잭션과 복잡한 멀티 스레드 상황을 컨테이너가 처리해준다는 점이다. 따라서 개발자는 싱글 스레드 애플리케이션처럼 단순하게 개발할 수 있고 비즈니스 로직 개발에 집중할 수 있다.   

### 스프링 컨테이너의 기본 전략
컨테이너 환경에서 JPA를 사용하면 컨테이너가 제공하는 전략을 따라야 한다. 스프링 컨테이너는 '트랜잭션 범위의 영속성 컨텍스트' 전략을 기본으로 사용한다. 이 전략은 트랜잭션을 시작할 때 영속성 컨텍스트를 생성하고 트랜잭션이 끝날 때 영속성 컨텍스트를 종료한다.

스프링 프레임워크에서는 서비스 계층에서 @Transactional 어노테이션을 선언해서 트랜잭션을 시작한다. 이 어노테이션이 있으면 호출한 메서드를 실행하기 직전에 스프링의 트랜잭션 AOP가 먼저 동작한다. 스프링 트랜잭션 AOP는 대상 메서드를 호출하기 직전에 트랜잭션을 시작하고, 대상 메서드가 정상 종료되면 트랜잭션을 커밋하면서 종료한다. 이 때 트랜잭션을 커밋하면 JPA는 영속성 컨텍스트를 flush해서 변경 내용을 데이터베이스에 반영한 후에 데이터베이스 트랜잭션을 커밋한다. 따라서 영속성 컨텍스트의 변경 내용이 데이터베이스에 정상 반영된다. 만약 예외가 발생하면 트랜잭션을 롤백하고 종료하는데 이때는 flush를 호출하지 않는다.

### 트랜잭션 범위의 영속성 컨텍스트
- 트랜잭션이 같으면 같은 영속성 컨텍스트를 사용한다.  
    트랜잭션 내에서 Repository마다 사용하는 엔티티 매니저가 달라도 같은 영속성 컨텍스트를 사용한다.

- 트랜잭션이 다르면 다른 영속성 컨텍스트를 사용한다.  
    여러 스레드에서 동시에 요청이 와서 같은 엔티티 매니저를 사용하면 트랜잭션에 따라 영속성 컨텍스트가 다르다. 스프링 컨테이너는 스레드마다 각각 다른 트랜잭션을 할당한다. 따라서 같은 엔티티 매니저를 호출해도 접근하는 영속성 컨텍스트가 다르므로 멀티스레드 환경에 안전하다.

## Application-managed
애플리케이션이 관리하는 엔티티 매니저  

애플리케이션 코드에서 엔티티 매니저를 제어하여 사용한다. 개발자가 엔티티 매니저 팩토리에서 엔티티 매니저를 매번 생성한다. 흐름을 세밀하게 제어할 수 있지만 그만큼 해야 할 일이 많아진다.

### @PersistenceUnit
```java
@PersistenceUnit //엔티티 매니저 팩토리 주입
private EntityManagerFactory entityManagerFactory;
```

@PersistenceUnit은 엔티티 매니저 팩토리를 주입하는 어노테이션이다. 애플리케이션이 관리하는 엔티티 매니저를 사용하면 엔티티 매니저를 닫는 작업(close)을 해줘야 하고, 커밋과 롤백 작업을 명시적으로 호출해줘야 한다. 

## 참고
자바 ORM 표준 JPA 프로그래밍 11장, 13장  
https://www.byteslounge.com/tutorials/container-vs-application-managed-entitymanager  
https://www.vincenzoracca.com/en/blog/framework/jpa/jpa-em/  