# Spring Boot

## 스프링 부트란?
Spring Boot는 Java 개발을 위한 Spring Framework의 사용을 단순화하는 것을 목표로 하는 프레임워크다. Spring Boot는 많은 설정을 자동으로 구성해주기 때문에 개발자가 비즈니스 로직 작성에만 집중할 수 있게 해준다. 예를 들어, 웹 애플리케이션을 실행하기 위해 Tomcat 서버를 설정할 필요가 없다. 내장된 Tomcat 서버가 제공되므로 애플리케이션으로 바로 실행할 수 있다.

## 주요 기능
다음은 Spring Boot 프레임워크의 주요 기능이다.

1. ```스타터 종속성(starter dependency)``` : 스타터 종속성은 종속성 관리를 처리한다. 예를 들어, Spring MVC 기반 RESTful 서비스를 개발하려는 경우 spring-boot-web-starter 종속성을 지정하면 자동으로 Spring MVC JAR, Jackson JAR 파일을 모두 다운로드한다. 스타터를 사용하면 많은 라이브러리의 종속성을 일일히 선언할 필요가 없어지고 호환성 및 버전 불일치 문제를 자동으로 해결할 수 있다.

2. ```자동 구성(auto configuration)``` : 스프링 부트는 클래스 경로에 있는 내용을 기반으로 많은 것을 자동으로 구성한다. 예를 들어, 스프링 웹 애플리케이션을 개발 중이고 클래스 경로에 Thymeleaf.jar가 있는 경우 Thymeleaf 템플릿 리졸버 및 기타 설정을 자동으로 구성할 수 있다. 자동 구성이 빈, 컨트롤러 등을 구성하는 것과 관련하여 많은 작업을 수행해주기 때문에 애플리케이션을 개발하는데 큰 도움이 된다. 자동 구성 기능을 활성화하려면 Main 클래스에서 @SpringBootApplication 또는 @EnableAutoConfiguration을 사용하면 된다.

3. ```스프링 이니셜라이저(Spring initializer)``` : https://start.spring.io/ 초기 프로젝트 설정을 단순화해준다.

4. ```스프링 액츄에이터(Spring Actuator)``` : 스프링 액츄에이터는 실행 중인 애플리케이션 내부를 볼 수 있는 기능이다. 예를 들어, 스프링의 애플리케이션 컨텍스트에서 생성된 빈과 컨트롤러에 매핑되는 요청 경로를 찾기 위해 액츄에이터를 사용할 수 있다. CPU 및 메모리 사용량, 스레드 수 등 다양한 메트릭도 확인할 수 있다.

5. ```Spring CLI``` : 스프링 CLI는 Groovy를 사용하여 스프링 기반 자바 애플리케이션을 생성할 수 있는 명령줄 인터페이스다. 

## @SpringBootApplication과 @EnableAutoConfiguration의 차이점
```@SpringBootApplication```과 ```@EnableAutoConfiguration```은 모두 스프링 부트 애플리케이션에 필수적이고 Main 클래스에서 사용되지만 둘 사이에는 미묘한 차이가 있다.  

- ```@EnableAutoConfiguration```은 자동 구성을 활성화하는 데 사용된다.
- ```@SpringBootApplication```은 ```@EnableAutoConfiguration```, ```@Configuration```, ```@ComponentScan```의 조합으로 구현되어 있다. 따라서 자동 구성뿐만 아니라 프로젝트에서 자바 기반의 구성 요소를 스캔하고 구성 요소를 활성화하는 일을 한다. 

## 스프링 부트 속성 정의
```application.properties``` 혹은 ```application.yml``` 파일에 애플리케이션과 스프링 부트의 속성을 정의할 수 있다. 예를 들어, ```application.properties``` 파일에 server.port라는 속성을 추가하여 스프링 부트의 임베디드 Tomcat 서버의 포트를 변경할 수 있다. 이 파일은 클래스 경로에 존재하는 경우 Spring Boot가 자동으로 로드해주기 때문에 로딩을 위한 별도의 설정을 할 필요가 없다.

## 임베디드 컨테이너와 WAR의 차이점
임베디드 컨테이너는 웹 서버를 설정하지 않고도 명령 프롬프트에서 JAR로 Spring Boot 애플리케이션을 사용할 수 있다는 것이다. 그러나 WAR 파일을 실행하려면 먼저 서블릿 컨테이너가 있는 Tomcat과 같은 웹 서버를 설정 한 다음 거기에 WAR을 배포해야 한다.  

Spring Boot는 Tomcat, Jetty 및 Undertow의 세 가지 임베디드 컨테이너를 지원한다. 기본적으로 Tomcat을 임베디드 컨테이너로 사용하지만 Jetty 또는 Undertow로 변경할 수 있다.
