# @Autowired vs @PersistenceContext for EntityManager

## @Autowired
```java
@Autowired
private EntityManager entityManager;
```

- @Autowired는 스프링에서 자동 주입을 위해 사용되는 어노테이션이다.
- 예전에는 불가능했지만, 이제는 스프링 부트에서 @Autowired로 엔티티 매니저의 자동 주입이 가능하다. 마찬가지로 생성자 주입도 가능하다.

## @PersistenceContext
```java
@PersistenceContext
private EntityManager entityManager;
```

- @PersistenceContext는 JPA 기본 어노테이션이다. 
- 프로젝트에서 서로 다른 데이터베이스에 연결되는 다중 데이터소스를 사용하는 경우, 설정을 통해 어떤 데이터베이스를 이용할지 정해줄 수 있다.

## 결론
엔티티 매니저를 주입할 때 @Autowired를 이용한 필드 주입, 생성자 주입, @PersistenceContext 모두 사용 가능하다.  

## 참고
https://stackoverflow.com/questions/31335211/autowired-vs-persistencecontext-for-entitymanager-bean/31335314  
https://lifedeveloper.tistory.com/entry/JPA%EC%8B%A4%EC%A0%84RequiredArgsConstructor-EntityManager%EC%97%90-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0  
