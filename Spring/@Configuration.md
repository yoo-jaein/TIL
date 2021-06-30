# @Configuration
하나 이상의 @Bean 메서드를 선언하는 설정 클래스에 붙이는 어노테이션

## @Bean
- 메서드의 리턴 객체를 IoC의 빈으로 등록한다.
- 빈의 이름은 기본적으로 메서드 이름으로 등록되며, @Bean(name="name")으로 이름을 변경할 수 있다.
- @Scope를 통해 객체 생성을 조정할 수 있다.
- init(), destroy() 등 라이프사이클 메서드를 추가한 후 @Bean에 지정할 수 있다.

## 코드
```java
@Configuration
 public class AppConfig {

     @Bean
     public MyBean myBean() {
         // instantiate, configure and return bean ...
     }
 }
```
빈 등록을 위한 어노테이션을 추가할 때 @Primary 어노테이션을 통해 빈의 우선권을 부여할 수 있고, @Qualifier("name") 어노테이션을 통해 특정 이름을 가지는 빈을 찾을 수 있다. 두 가지 어노테이션을 동시에 사용했을 경우 @Qualifier가 우선권을 가진다.    

## 주의!
@Bean 어노테이션만으로도 스프링 컨테이너에 빈 등록이 가능하다. 하지만 이 때는 싱글톤을 보장 받지 못한다.

## 참고
https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Configuration.html  
https://ch4njun.tistory.com/219  