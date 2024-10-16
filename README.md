# Today I Learned ✏

매일 조금씩 지식이 쌓이는 곳.

__오타, 오역, 잘못된 정보가 있다면 알려주세요.__

## 📌 INDEX

### Architecture
- Monolithic vs Microservices : 모놀리식과 마이크로서비스 아키텍처
- Handling high traffic : 대용량 트래픽 처리하기
- MVC : MVC 아키텍처

### AWS
- Autoscaling : 로드 밸런서와 AWS EC2의 오토스케일링
- Container : AWS의 가상화/컨테이너 도구
- Database : AWS의 데이터베이스 종류(RDS, Aurora, DynamoDB, ElastiCache 등)와 마이그레이션
- Deploy Automation : AWS의 배포 자동화 도구(CloudFormation)
- EC2 : AWS EC2
- IAM : AWS IAM
- Instance Scheduler : AWS Instance Scheduler
- Monitoring : AWS의 모니터링 도구(CloudWatch, CloudTrail)
- Storage : AWS의 스토리지(EBS, S3)
- VPC : AWS VPC

### Database
- Database connection pool : 데이터베이스 커넥션 풀
- RDB vs NoSQL : RDB vs NoSQL
- System design for heavy read : Read 부하를 줄이기 위한 시스템 디자인하기

### Database-DynamoDB
- DynamoDB : DynamoDB
- DynamoDB review : DynamoDB 사용 후기

### Database-MySQL
- B-Tree : B-Tree, B+Tree
- Index : 인덱스
- MySQL auto_increment and Rollback : MySQL에서 트랜잭션 롤백이 일어날 때 auto_increment 컬럼의 값은 어떻게 될까?
- MySQL Engine Architecture : MySQL 엔진 아키텍처(MySQL 엔진과 스토리지 엔진)
- MySQL InnoDB : MySQL InnoDB 스토리지 엔진의 특징
- MySQL InnoDB Clustering Index : MySQL InnoDB 클러스터링 인덱스(프라이머리 키 인덱스)
- MySQL Lock : MySQL 잠금(MySQL 엔진 레벨의 락과 스토리지 엔진 레벨의 락)
- MySQL query execution : MySQL 쿼리 실행 과정
- Partitioning and Sharding : 파티셔닝과 샤딩
- Show create table : MySQL에서 테이블의 생성 쿼리 알아내기
- Transaction : 트랜잭션

### Database-Redis
- Embedded Redis : 테스트 코드를 위한 임베디드 레디스
- How to implement Redis repository : Redis 저장소를 구현하는 방법
- Redis : Redis
- Redis data types : Redis 데이터 타입 알아보기
- RedisTemplate.opsForValue() : RedisTemplate.opsForValue()
- Spring Data Redis : 스프링 데이터 Redis

### Elasticsearch
- Elasticsearch : 일래스틱서치
- QueryBuilder : QueryBuilder로 쿼리 구성하기
- Using Elasticsearch with Spring Boot : 스프링 부트 프로젝트에서 일래스틱서치 쓰기

### Infra
- Cloud : 클라우드 컴퓨팅의 분류와 특징
- DevOps : 데브옵스의 등장 배경, 데브옵스와 도커의 관계
- Immutable infrastructure : 불변 인프라의 등장 배경과 개념
- Load Balancing : 로드 밸런싱
- Server Virtualization : 하이퍼바이저형 가상화와 컨테이너형 가상화의 차이
- Virtualization : 가상화

### IntelliJ
- How to fix CheckStyle warnings : 인텔리제이에서 CheckStyle 경고를 해결하는 방법
- IntelliJ IDEA Shortcuts : 알아두면 좋은 인텔리제이 단축키 모음

### Language

### Language-Java
- Apache tika : Apache tika 라이브러리
- Arrays.asList() UnsupportedOperationException : Arrays.asList() 사용 시 UnsupportedOperationException 발생
- Boolean.getBoolean() vs parseBoolean() : Boolean.getBoolean()과 Boolean.parseBoolean()의 차이점
- Collections.singletonList() vs List.asList() : Collections.singletonList()와 List.asList()의 차이점
- Custom annotation : 커스텀 어노테이션
- Flow : Flow
- Garbage Collection : 가비지 컬렉션
- Gradle vs Maven : Gradle vs Maven
- How to measure execution time for a Java code : 자바 코드의 실행시간 측정하기
- Jackson : Jackson 라이브러리
- Instant : java.time.Instant
- Java command line options : 자바 커맨드 라인 옵션
- List.isEmpty() vs List.size() : 자바 List의 원소 유무를 알아내는 방법
- log4j Logging level : log4j의 로그 레벨과 레벨에 따른 작동 방식
- Method Overloading : 자바 메서드 오버로딩의 조건과 개념, 주의 사항
- OpenCSV : .csv 파일 파싱을 위한 자바 라이브러리
- Primitive type vs Reference type : 자바 데이터 타입 중 기본형과 참조형
- Reflection : 자바 Reflection API
- References : 자바 참조
- Serialization and Deserialization : 자바 직렬화와 역직렬화
- Unix epoch time : 자바에서 Unix epoch time 구하기

### Linux
- Command : 자주 쓰는 리눅스 명령어 모음
- vi : 리눅스 vi 편집기의 모드와 명령어 모음

### Spring
- @Configuration : 스프링 설정 클래스의 @Configuration
- @DateTimeFormat : @DateTimeFormat
- @Transactional : @Transactional, 스프링의 트랜잭션
- application.yml : application.yml
- Automating the generation of API documentation : API 문서 자동화
- Asynchronous Spring MVC : Spring MVC로 비동기 프로그래밍하기
- Controller and Service : 스프링 컨트롤러와 서비스의 개념
- EhCache : 스프링 부트 환경에서 Ehcache 사용법
- Entity and DTO : 엔티티와 DTO의 개념, DTO를 반드시 사용해야 하는 이유
- Error and Exception : 자바 Error와 Exception의 개념, 예외 처리 방법
- Handling Exception : 스프링 예외 처리하기
- Hot vs Cold Stream : 핫 스트림과 콜드 스트림
- HttpServletRequest : HttpServletRequest의 사용법
- Improving Spring WebFlux performance : 스프링 웹플럭스 애플리케이션의 성능 개선하기
- Interceptor vs Filter vs AOP : 인터셉터 vs 필터 vs AOP
- Inversion of Control with Dependency Injection : 헷갈리기 쉬운 IoC와 DI
- Logback : 스프링 부트 환경에서 Logback 사용법
- MaxUploadSizeExceededException : MaxUploadSizeExceededException 예외 해결하기
- POJO framework : POJO(Plain Old Java Object) 프레임워크
- Reactive Programming and Spring WebFlux : 반응형 프로그래밍과 스프링 웹플럭스
- Refactoring DAO : 토비의 스프링 3.1 Vol.1 1장(DAO 리팩터링하기)
- RestTemplate vs WebClient : RestTemplate와 WebClient 비교하기
- Spring : 스프링 프레임워크
- Spring AOP : 스프링 AOP
- Spring Boot : 스프링 부트
- Spring boot test class annotation : 스프링 부트의 테스트 클래스 어노테이션 종류(@SpringBootTest, @WebMvcTest, @DataJpaTest)
- Spring IoC Container : 스프링 빈 팩토리와 애플리케이션 컨텍스트 용어 설명
- Spring MVC Request Lifecycle : 스프링 MVC 요청 라이프사이클(DispatcherServlet, HandlerMapping, Controller, HandlerAdapter, Interceptor, View, ViewResolver)
- Spring MVC vs Spring WebFlux : 스프링 MVC와 스프링 웹플럭스 비교하기
- Why use service and serviceimpl : 스프링으로 개발할 때 서비스 인터페이스와 서비스 구현 클래스를 두는 이유

### Spring-Batch
- Batch application management : 스프링 배치 애플리케이션을 운영하는 방법
- Chunk-Oriented Processing : 청크 지향 프로세싱
- Chunksize : 청크 크기 정의하기
- Cron expression : 크론 표현식
- High Performance Batch Processing : 고성능 일괄 처리
- Job : 잡
- JobExecutionListener : 잡 리스너
- JobParameters : 잡 파라미터
- JobRepository : JobRepository 스키마와 6개의 테이블
- JpaPagingItemReader : JpaPagingItemReader
- ItemReader : ItemReader 종류
- ItemWriter : ItemWriter 종류
- Logging Item Reading Processing Writing : 스프링 배치에서 Item을 Read, Process, Write할 때 로그를 찍는 방법
- RepositoryItemReader : RepositoryItemReader
- Spring Batch : 스프링 배치 프레임워크
- Spring Batch Exception Handling : 스프링 배치에서 에러 핸들링하는 세 가지 방법
- Spring Batch Test BeanCreationException : 스프링 배치에서 Job을 테스트할 때 발생되는 BeanCreationException을 해결하는 방법
- Step : 스텝
- StepExecutionListener : 스텝 리스너
- Tasklet Step : Tasklet 스텝

### Spring-JPA
- @Autowired vs @PersistenceContext for EntityManager : 스프링 부트에서 엔티티 매니저를 주입받는 방법
- @Modifying : 스프링 데이터 JPA에서 @Query와 함께 사용되는 @Modifying
- Container vs Application managed EntityManager : 컨테이너가 관리하는 엔티티 매니저와 애플리케이션이 관리하는 엔티티 매니저의 차이, 스프링 컨테이너의 영속성 컨텍스트 기본 전략
- DTO and mapping : 엔티티와 DTO의 변환 메서드, 변환 시 주의 사항
- EntityManager and EntityManagerFactory : 엔티티 매니저 팩토리와 엔티티 매니저의 관계
- find() vs JPQL : find() 메서드와 JPQL의 내부 동작 방식 차이, 결론
- findById() vs getOne() :  findById()와 getOne()의 차이점, 메서드 이름에서 find-와 get-의 구분
- First level cache and Second level cache in Hibernate : 1차 캐시와 2차 캐시
- Hibernate : 하이버네이트
- How to concat String and stringValue() : Querydsl에서 문자열 String과 stringValue()를 연결하는 방법
- How to get JPAQueryFactory bean : Querydsl에서 쿼리를 만들 때 필요한 JPAQueryFactory를 받아오는 방법
- Improve readability of Querydsl : Querydsl 코드의 가독성을 높이는 방법
- JPA and ORM : JPA, ORM, Hibernate의 개념 정리와 JPA를 사용해야 하는 이유
- JPA Entity Mapping : JPA 엔티티 매핑하기
- JPA FetchType : JPA 즉시 로딩(Eager Loading)과 지연 로딩(Lazy Loading)
- JpaRepository and SimpleJpaRepository : JpaRepository와 그 구현체인 SimpleJpaRepository 들여다보기
- JPQL : 객체 지향 쿼리 JPQL
- N+1 Query problem in JPA : N+1 문제의 발생 원인과 해결 방법
- Persistence Context : 영속성 컨텍스트의 개념과 장점, 영속성 컨텍스트가 실제 데이터베이스에 반영되는 시점은?
- Query method, JPQL or Querydsl : Repository를 개발할 때 쿼리 메서드, JPQL, Querydsl과 같은 JPQL 빌더 라이브러리를 사용할 수 있다. 이 중에 가장 효과적인 방법은?
- Querydsl Fetchable\<T> : Querydsl에서 결과를 반환하는 메서드의 종류(fetch(), fetchFirst() 등)
- QuerydslRepositorySupport : QuerydslRepositorySupport
- Refactor JPQL to Querydsl : @Query로 작성된 JPQL을 Querydsl 코드로 재구성하기
- Update multiple columns in Querydsl : Querydsl에서 Update문을 작성할 때 여러 컬럼을 set()하려면 어떻게 해야 할까?

### Spring-Test
- @MockBean : 스프링 부트 테스트 어노테이션 @MockBean
- @Sql : @Sql
- JUnit4 annotations : 자바 테스트 프레임워크 JUnit4의 어노테이션
- Mock : 목(Mock) 객체

### Vue
- File validation : 파일 검사하기(파일 형식, 용량)

### Web
- Ajax : Ajax의 등장 배경과 개념
- Cookie and Session : 쿠키와 세션의 개념, 동작 방식, 차이
- CORS : CORS(Cross-Origin Resource Sharing)
- HTTP : HTTP 프로토콜
- HTTP Status Code : HTTP 상태 응답 코드 모음
- JWT : JSON Web Token(JWT)
- Single Page Application : 서버사이드 렌더링과 클라이언트사이드 렌더링의 차이로 보는 싱글 페이지 애플리케이션(Single-Page Application)
- TCP Connection : TCP 커넥션
- Timeout : 타임아웃의 종류
- Web server vs Web Application Server : 웹서버와 WAS의 차이점
