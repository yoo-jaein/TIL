# EntityManager and EntityManagerFactory

## 엔티티 매니저 팩토리
```java
//엔티티 매니저 팩토리 생성
EntityManagerFactory emf = Persistence.createEntityManagerFactory("jpabook");
```

- JPA를 시작하려면 우선 persistence.xml의 설정 정보를 사용해서 팩토리를 만들어야 한다. 이 때 엔티티 매니저 팩토리를 생성해서 JPA를 사용할 수 있게 준비해주는 Persistence 클래스를 사용한다.
- 위 코드에서는 persistence.xml의 설정 정보를 읽어서 이름이 jpabook인 영속성 유닛(persistence unit)을 찾아서 엔티티 매니저 팩토리를 생성한다.
- Hibernate를 포함한 JPA 구현체들은 엔티티 매니저 팩토리를 만들 때 커넥션풀도 함께 생성한다.
- 엔티티 매니저 팩토리는 이름 그대로 엔티티 매니저를 만드는 공장이다. 공장을 만드는 비용은 매우 크다. 따라서 엔티티 매니저 팩토리는 애플리케이션 전체에서 하나를 공유하도록 설계되어 있다. 엔티티 매니저 팩토리는 여러 스레드가 동시에 접근해도 안전하므로 서로 다른 스레드 간에 공유해도 된다.
- 애플리케이션을 종료할 때 엔티티 매니저 팩토리도 emf.close()처럼 종료해야 한다.

## 엔티티 매니저
```java
//엔티티 매니저 생성
EntityManager em = emf.createEntityManager();
```
- 엔티티 매니저는 엔티티를 저장하고, 수정하고, 삭제하고, 조회하는 등 엔티티와 관련된 모든 일을 처리한다.
- 애플리케이션이 영속성 컨텍스트와 상호작용하기 위한 하나의 인터페이스이다. 내부에 데이터소스(datasource, 데이터베이스 커넥션)를 유지하면서 데이터베이스와 통신한다.
- 엔티티 매니저는 데이터베이스 연결이 꼭 필요한 시점까지 커넥션을 얻지 않는다. 보통 트랜잭션을 시작할 때 커넥션을 획득한다.
- 엔티티 매니저는 여러 스레드가 동시에 접근하면 동시성 문제가 발생하므로 스레드 간에 절대 공유하면 안 된다. 
- 엔티티 매니저 팩토리가 닫히면(closed) 모든 엔티티 매니저는 닫힌 상태(closed state)로 간주된다.
- 사용이 끝난 엔티티 매니저는 em.close()처럼 반드시 종료해야 한다.

## Spring에서 엔티티 매니저
https://github.com/yoo-jaein/TIL/blob/main/JPA/Container_vs_Application_managed_EntityManager.md

## 참고
자바 ORM 표준 JPA 프로그래밍 3장  
https://kihoonkim.github.io/2017/01/27/JPA(Java%20ORM)/2.%20JPA-%EC%98%81%EC%86%8D%EC%84%B1%20%EA%B4%80%EB%A6%AC/  
https://www.javaguides.net/2018/12/jpa-entitymanagerfactory-interface-with-example.html  
https://www.javaguides.net/2018/12/jpa-entitymanager-interface-with-example.html  
