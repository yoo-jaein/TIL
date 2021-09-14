# Spring boot test class annotation
스프링 부트의 테스트 클래스 어노테이션 종류

## @SpringBootTest
스프링 부트 애플리케이션을 실행할 때 스프링 컨테이너를 시작하는 것과 똑같이 전체 컨텍스트를 로드한다. 테스트의 목 설정과 관계 없이 모든 빈 객체를 초기화하고 주입한다.  

- 로컬에서 통합 테스트를 할 때 사용
- 임베디드 데이터베이스를 사용하고, 전체 컨텍스트를 로드하는 테스트를 할 때 @AutoConfigureTestDatabase와 함께 사용  
- MockMVC를 사용하고, 전체 컨텍스트를 로드하는 테스트를 할 때 @AutoConfigureMockMvc와 함께 사용

## @WebMvcTest
Spring MVC 컴포넌트만 로드한다. Request/Response 처리를 위한 Spring Security, Filter, MockMVC 등이 포함된다. @Component, @Service, @Repository 빈은 포함되지 않는다. 로드하는 컴포넌트 수가 @SpringBootTest보다 적기 때문에 속도가 더 빠르다.  

- @Controller, @RestController를 테스트할 때 사용
- JUnit4를 사용하고 컨트롤러를 테스트할 때 @RunWith(SpringRunner.class)와 함께 사용

## @DataJpaTest
JPA에 대한 구성만 로드한다. 엔티티들과 EntityManager 정도만 등록되어 테스트된다. 별도의 설정을 지정하지 않는 경우 임베디드 메모리 데이터베이스인 H2를 사용한다. 내부적으로 @Transactional이 적용되어 있어 테스트가 끝날 때마다 트랜잭션을 롤백한다.  

- JPA Repository를 테스트할 때 사용
- JUnit4를 사용하고 JPA를 테스트할 때 @RunWith(SpringRunner.class)와 함께 사용

## 참고
https://stackoverflow.com/questions/59097035/springboottest-vs-webmvctest-datajpatest-service-unit-tests-what-is-the-b  
https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/test/context/SpringBootTest.html  
https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/test/autoconfigure/web/servlet/WebMvcTest.html  
https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/test/autoconfigure/orm/jpa/DataJpaTest.html  
