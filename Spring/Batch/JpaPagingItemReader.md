# JpaPagingItemReader
JPA는 자바 ORM 기술에 대한 API 표준 명세다. JpaPagingItemReader는 JPA를 사용하는 환경에서 페이징 기법으로 데이터를 가져올 때 사용한다. 참고로, JPA는 데이터를 조회할 때 커서 기반 방법은 제공하지 않는다.  

spring-boot-starter-data-jpa를 사용하는 모든 애플리케이션에는 스프링 배치에서 JPA를 사용하는 데 필요한 필수 컴포넌트가 포함돼 있다.  

## 구성
```java
private final EntityManagerFactory entityManagerFactory;
private static final String JOB_NAME = "customerItem";
//...
@Bean
@StepScope
public JpaPagingItemReader<Customer> customerItemReader(@Value("#{jobParameters['city']}") String city) {
    return new JpaPagingItemReaderBuilder<Customer>()
        .name(JOB_NAME + "Reader") // (1)
        .entityManagerFactory(entityManagerFactory) // (2)
        .queryString("select c from Customer c where c.city = :city") // (3)
        .parameterValues(Collections.singletonMap("city", city)) // (4)
        .build();
}
```
JpaPagingItemReader에는 네 개의 의존성이 필요하다.

1. ExecutionContext 내 엔트리의 접두어로 사용되는 이름
2. 스프링 부트가 제공하는 엔티티 매니저
3. 실행할 쿼리
4. 파라미터

## 참고
스프링 배치 완벽 가이드 2/e  