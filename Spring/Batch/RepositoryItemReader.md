# RepositoryItemReader
스프링 데이터는 기본적으로 인터페이스를 제공한다. 이 중 하나를 상속하는 커스텀 인터페이스를 만들고(Custom Repository) 메서드를 선언만 해주면(Spring Data Query method) 스프링 데이터가 알아서 적절한 쿼리를 생성해준다.   

스프링 배치는 스프링 데이터의 인터페이스 중 PagingAndSortingRepository를 활용하기 때문에 호환성이 좋다. RepositoryItemReader는 JdbcPagingItemReader나 HibernatePagingItemReader를 사용할 때와 동일한 방식으로 PagingAndSortingRepository를 사용해서 페이징 쿼리를 실행한다. 그래서 RepositoryItemReader를 사용하면 스프링 데이터가 지원하는 데이터 저장소라면 어디든지 질의를 수행할 수 있다.

## 구성
### 리포지토리
```java
public interface CustomerRepository extends JpaRepository<Customer, Long> {
    Page<Customer> findByCity(String city, Pageable pageRequest);
}
```
스프링 데이터가 제공하는 페이징 매커니즘은 활용할 수 없다. 페이징 기능이 작동하게 하려면 Pageable 파라미터를 직접 추가해야 한다. 위의 리포지토리의 구현체는 데이터를 한 페이지만큼 요청하는 데 필요한 파라미터를 캡슐화한다. 구체적으로는 페이지 크기, 오프셋, 페이지 번호, 정렬 옵션, 기타 페이징 쿼리를 빌드하는데 필요한 매커니즘이 포함된다.  

또한, 메서드의 반환 타입도 List<Customer>가 아닌 Page<Customer>로 변경한다. Page 객체에는 해당 페이지의 실제 데이터, Pageable 객체 값, 메타 정보 등이 담겨있다.

만약 프로젝트에 여러 종류의 스프링 데이터 스타터가 포함되어 있을 경우, JpaRepository 같은 전용 인터페이스를 사용해서 소속을 표시해야 한다.

### Reader
```java
private final CustomerRepository repository;
private static final String JOB_NAME = "customerItem";
//...
@Bean
@StepScope
public RepositoryItemReader<Customer> customerItemReader(@Value("#{jobParameters['city']}") String city) {
    return new RepositoryItemReaderBuilder<Customer>()
        .name(JOB_NAME + "Reader")
        .arguments(Collections.singletonList(city))
        .methodName("findByCity")
        .repository(repository)
        .sorts(Collections.singletonMap("lastName", Sort.Direction.ASC))
        .build();
}
```
RepositoryItemReader에 다섯 개의 의존성을 추가한다.

1. 이름
2. Pageable 파라미터를 제외한 메서드 아규먼트
3. 호출할 메서드 이름
4. 리포지토리 구현체
5. 필요한 정렬 방법

## 참고
스프링 배치 완벽 가이드 2/e  