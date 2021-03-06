# application.yml
스프링 부트의 외부 설정 파일 application.yml 사용하기

## 사용법
```yaml
school:
  grade: 2
  class: 11
  teacher:
    firstName: Jaein
    lastName: Yoo
    fullName: ${school.teacher.lastName} ${school.teacher.firstName}
```

- application 파일에 정의된 값을 변수처럼 사용할 수 있다.

```java
@Value("${school.grade}")
private int grade;
```

- @Value 어노테이션으로 설정 파일의 값을 읽어올 수 있다.

```yaml
spring:
  profiles:
    active: local

---
spring:
  profiles: local
logging:
  level:
    #...

---
spring:
  profiles: dev
logging:
  level:
    #...
```

- profiles.active를 통해 적용할 profile을 선택할 수 있다.
- '---'을 사용하면 하나의 파일에서 여러 profile을 정의할 수 있다.
- '#'을 사용하면 주석을 작성할 수 있다.

```java
@TestPropertySource(properties = {"school.class=3"})
public class SchoolTest {
    //...
```

- 설정 파일의 프로퍼티 값을 변경하고 싶다면 프로퍼티 우선순위를 참고하여 오버라이딩한다.
- https://docs.spring.io/spring-boot/docs/1.5.22.RELEASE/reference/html/boot-features-external-config.html

## 중요! 스프링 부트 2.4.0 이후 업데이트
- [spring.io - Config file processing in spring boot 2.4](https://spring.io/blog/2020/08/14/config-file-processing-in-spring-boot-2-4)  

- 간단한 프로젝트에서 application 파일을 하나만 사용하는 경우는 상관없지만, 여러 profile별 application 파일을 다르게 구성하는 경우 위의 변경 사항을 잘 읽어봐야 한다.
- 이 경우 위에 기재한 spring.profiles 대신 spring.config.activate.on-profile을 사용해야 한다. 