# EntityManager and EntityManagerFactory

## 엔티티 매니저 팩토리
```java
//엔티티 매니저 팩토리 생성
EntityManagerFactory emf = Persistence.createEntityManagerFactory("jpabook");

//엔티티 매니저 생성
EntityManager em = emf.createEntityManager();
```

엔티티 매니저 팩토리는 이름 그대로 엔티티 매니저를 만드는 공장이다. 공장을 만드는 비용은 매우 크다. 따라서 엔티티 매니저 팩토리는 애플리케이션 전체에서 하나를 공유하도록 설계되어 있다. 엔티티 매니저 팩토리는 여러 스레드가 동시에 접근해도 안전하므로 서로 다른 스레드 간에 공유해도 된다.  

엔티티 매니저 팩토리가 닫히면(closed) 모든 엔티티 매니저는 닫힌 상태(closed state)로 간주된다. 

## 엔티티 매니저
엔티티 매니저는 엔티티를 저장하고, 수정하고, 삭제하고, 조회하는 등 엔티티와 관련된 모든 일을 처리한다. 애플리케이션이 영속성 컨텍스트와 상호작용하기 위한 하나의 인터페이스이다.   

엔티티 매니저는 여러 스레드가 동시에 접근하면 동시성 문제가 발생하므로 스레드 간에 절대 공유하면 안 된다.

## Spring에서 엔티티 매니저


## 참고
자바 ORM 표준 JPA 프로그래밍 3장  
https://kihoonkim.github.io/2017/01/27/JPA(Java%20ORM)/2.%20JPA-%EC%98%81%EC%86%8D%EC%84%B1%20%EA%B4%80%EB%A6%AC/  
https://www.javaguides.net/2018/12/jpa-entitymanagerfactory-interface-with-example.html  
https://www.javaguides.net/2018/12/jpa-entitymanager-interface-with-example.html  
