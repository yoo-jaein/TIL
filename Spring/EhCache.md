# EhCache
스프링 부트 환경에서 Ehcache 사용법

## Ehcache
Ehcache는 자바 기반의 오픈소스 캐시 라이브러리다. 

## 특징
1. 속도  
  Ehcache의 동시성 기능은 대규모 동시성 시스템을 위해 설계되었다. 
2. 경량  
  코어를 사용하기 위한 의존성에는 SLF4J만 존재한다.
3. 표준 기반  
  Ehcache 3.x는 JCache API (JSR-107) 표준을 기반으로 개발되었다.

## 스프링 부트의 캐싱
스프링 부트 환경에서 @EnableCaching 어노테이션을 통해 캐싱을 활성화할 수 있다.

```java
@Component
public class BookService {
	@Cacheable("price")
	public long computeFinalPrice(int id) {
		//...
	}
}
```

### 프로바이더
- org.springframework.cache.Cache  
- org.springframework.cache.CacheManager  

스프링 부트는 다음의 캐시 프로바이더를 지원한다. CacheManager 타입이나 CacheResolver 타입의 빈이 감지되지 않으면 표시된 순서대로 프로바이더를 감지한다.

1. Generic
2. **JCache** (JSR-107) (**Ehcache 3.x**, Hazelcast, Infinispan 등)
3. EhCache 2.x
4. Hazelcast
5. Infinispan
6. Couchbase
7. Redis
8. Caffeine
9. Simple

## Ehcache 3.x와 JCache (JSR-107)

### JCache
JCache라고도 하는 JSR-107(Java Temporary Caching API)은 javax.cache API를 정의하는 스펙(Specification, 소프트웨어 구현이 아님)이다. 이 스펙은 Java Community Process에서 개발되었으며 그 목적은 Java 애플리케이션에 대한 표준화된 캐싱 컨셉과 메커니즘을 제공하는 것이다.  

JCache API는 벤더 중립적인 캐싱 표준을 위해 설계되었다. 따라서 JCache API를 사용하여 애플리케이션을 만드는 경우, 다른 벤더의 캐싱 라이브러리로 변경하기가 쉽다. 애플리케이션 단에서 캐싱 관련 코드를 다시 작성하지 않아도 새로운 캐싱 솔루션을 적용할 수 있다.  

애플리케이션에서 JCache API 호출을 사용하려면 다음 jar 파일이 모두 필요하다.

1. JCache API를 정의하는 JCache jar  
2. JCache API를 구현하는 캐싱 프로바이더 jar (Ehcache jar)  

Ehcache jar는 JCache API 호출을 해당 Ehcache API로 변환해준다. 따라서, 개발자는 Ehcache API를 직접 사용할 필요 없이 표준 JCache API를 사용해서 개발하면 된다.

## 1. Ehcache 의존성 추가하기

spring-boot-starter-cache
기본 캐싱 종속성을 추가한다.

JCache, Ehcache 2.x, Caffeine을 사용하려면 spring-context-support를 추가한다.

### build.gradle
```groovy
implementation 'org.springframework.boot:spring-boot-starter-cache'
implementation 'org.ehcache:ehcache'
implementation 'javax.cache:cache-api'
```

## 2. @EnableCache
```java
@EnableCaching
@SpringBootApplication
public class DemoApplication {

  public static void main(String[] args) {
    SpringApplication.run(DemoApplication.class, args);
  }

}
```

@EnableCaching은 Spring의 캐시 관리를 활성화하는 어노테이션이다. 아래처럼 별도의 설정 파일을 만들어 적용시킬 수도 있다.

```java
@Configuration
@EnableCaching
public class CacheConfig {
  ...
}
```

## 3. application.yml
```yaml
spring:
  cache:
    jcache:
      config: classpath:ehcache.xml
```

Ehcache는 ehcache.xml 파일 혹은 자바 코드로 환경설정을 할 수 있다. xml 방식을 사용하는 경우, Spring이 Ehcache 설정 파일인 ehcache.xml을 찾을 수 있도록 속성을 추가한다. 

## 4. 캐시 적용


## 참고
https://medium.com/finda-tech/spring-%EB%A1%9C%EC%BB%AC-%EC%BA%90%EC%8B%9C-%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC-ehcache-4b5cba8697e0  
https://www.ehcache.org/  
https://www.ehcache.org/documentation/3.9/107.html  
https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.caching  
https://springframework.guru/using-ehcache-3-in-spring-boot/  