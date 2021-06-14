# How to get JPAQueryFactory bean
Querydsl에서 쿼리를 만들 때 필요한 JPAQueryFactory를 받아오는 방법  

## @Bean 설정하기

### 방법 1 : @SpringBootApplication 클래스에서 @Bean 설정
```java
@EnableJpaAuditing
@SpringBootApplication
public class JpaShopApplication {
    
    public static void main(String[] args) {
        SpringApplication.run(JpaShopApplication.class, args);
    }
    
    @Bean
    public JPAQueryFactory jpaQueryFactory(EntityManager entityManager) {
        return new JPAQueryFactory(entityManager);
    }
}
```

### 방법 2 : config 클래스를 추가하여 @Bean 설정 (추천)

```java
@Configuration
public class QuerydslConfig {

    @PersistenceContext
    private EntityManager entityManager;

    @Bean
    public JPAQueryFactory jpaQueryFactory() {
        return new JPAQueryFactory(entityManager);
    }
}
```

@Bean 설정을 통해 프로젝트 어디에서든 JPAQueryFactory를 주입받아 사용할 수 있다.

## JPAQueryFactory 받아오기

## 방법 1 : QuerydslRepositorySupport 상속
```java
@Repository
public class MemberRepositorySupport extends QuerydslRepositorySupport {
    
    private final JPAQueryFactory jpaQueryFactory;
    
    public MemberRepositorySupport(JPAQueryFactory jpaQueryFactory) {
        super(Member.class);
        this.jpaQueryFactory = jpaQueryFactory;
    }
    
    public List<Member> findMembers(int age) {
        return jpaQueryFactory
                .select(member)
                ...
    }
}
```
Bean 등록된 jpaQueryFactory를 생성자 인젝션으로 주입받아 사용한다. QuerydslRepositorySupport를 상속받으면 생성자를 설정해야 한다. 생성자에서 super(Domain.class);를 설정하고 JPAQueryFactory를 사용한다.  

이 방법으로도 Querydsl을 사용할 수 있지만 단점이 있다. 항상 2개의 Repository를 의존성으로 받아야 한다는 것이다. 

## 방법 2 : Custom Repository 적용 (추천)

```java
public interface MemberRepositoryCustom {
    List<Member> findByName(String name);
}
```
Custom 인터페이스를 생성한다.

```java
@RequiredArgsConstructor
public class MemberRepositoryImpl implements MemberRepositoryCustom {
    
    private final JPAQueryFactory jpaQueryFactory;

    public List<Member> findByName(String name) {
        return jpaQueryFactory
                .select(member)
                ...
    }
}
```
Custom 인터페이스를 구현하는 Impl 클래스를 생성한다.

```java
public interface MemberRepository extends JpaRepository<Member, Long>, MemberRepositoryCustom {
}
```
구현한 코드를 쓸 수 있도록 MemberRepository에서 상속해준다.

```java
memberRepository.findByName(name);
```
이 방법을 통해 Repository의 의존성을 줄일 수 있다.

## 방법 3 : Querydsl만으로 Repository 구성

```java
@RequiredArgsConstructor
@Repository
public class MemberRepository {
    
    private final JPAQueryFactory jpaQueryFactory;
    
    public List<Member> findByName(String name) {
        return jpaQueryFactory
                .select(member)
                ...
    }
}
최소한의 Bean 등록을 위해 @Repository를 선언해준다. 이후 별도의 상속, 구현 없이 JPAQueryFactory만으로 쿼리를 작성한다. 

```

## 참고
https://jojoldu.tistory.com/372  
https://jessyt.tistory.com/51  
https://velog.io/@woo0_hooo/Spring-Boot%EC%99%80-QueryDSL-%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0    
