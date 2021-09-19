# Logback
Logback은 Java 애플리케이션용 로깅 프레임워크다. 로그를 콘솔, 파일, DB 등에 남길 수 있으며 아래의 세 가지 모듈로 구성된다.

- logback-core : 기본 로깅 기능
- logback-classic : slf4j 지원과 같은 추가 로깅 개선 사항 포함
- logback-access : Tomcat 및 Jetty와 같은 서블릿 컨테이너와의 통합 제공

## logback 의존성 추가하기
스프링 부트에서 Logback을 추가하는데 두 가지 방법이 존재한다. 첫 번째 방법은 spring-boot-starter-logging을 추가하는 방법이다. 두 번째로 스프링 부트 웹 애플리케이션의 경우, spring-boot-starter-web 의존성에 logback-classic, logback-core가 이미 포함되어 있기 때문에 별도의 설정을 하지 않아도 된다.  

### pom.xml
```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

### build.gradle
```groovy
	implementation 'org.springframework.boot:spring-boot-starter-web'
```

## logback-contrib 의존성 추가하기
Logback에 대한 커뮤니티 지원 확장 프로젝트인 [logback-contrib](https://github.com/qos-ch/logback-contrib)를 사용해야 한다면 관련된 의존성을 추가해야 한다.

- jackson : JSON 형식의 로그 메시지를 렌더링하기 위해 Jackson JSON 프로세서를 활성화한다.
- json : JSON 형식의 로그 메시지를 활성화한다.
- eclipse : Logback이 Eclipse 플러그인에서 Eclipse의 오류 로그에 로그 메시지를 게시할 수 있도록 한다.
- mongodb : Logback이 NoSQL 데이터베이스인 MongoDB에 로그 메시지를 게시할 수 있도록 한다.

### build.gradle
```groovy
	implementation('ch.qos.logback.contrib:logback-jackson:0.1.5') //jackson
	implementation('ch.qos.logback.contrib:logback-json-classic:0.1.5') //json
```

## 간단한 로깅 설정
application.yml, application.properties에 간단한 설정을 통해 로그 수준과 로그 파일의 위치, 이름 등을 변경할 수 있다. https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#application-properties.core 에서 설정 가능한 목록을 확인할 수 있다.

### application.yml
```yaml
logging:
  level:
    org.springframework.web: "debug"
    org.hibernate: "error"
  file:
    name: "myapp.log"
```

### application.properties
```properties
logging.level.org.springframework.web=debug
logging.level.org.hibernate=error
logging.level.file.name=myapp.log
```

## logback-spring.xml
좀 더 세부적인 설정을 구성하려면 별도의 파일을 만들어야 한다. 클래스패스의 루트(src/main/resources/)에 ```logback.xml``` 파일을 추가할 수 있다. 스프링 부트를 사용하면 ```logback-spring.xml```을 사용할 수도 있다. [logback-spring.xml](https://github.com/yoo-jaein/TIL/blob/main/Spring/logback-spring.xml.md) 참고

## 참고
https://stackify.com/logging-logback/  
https://docs.spring.io/spring-boot/docs/current/reference/html/howto.html#howto.logging  
http://logback.qos.ch/manual/configuration.html  