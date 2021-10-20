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

## 3.1 Ehcache.xml
```xml
<config xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'
        xmlns='http://www.ehcache.org/v3'
        xsi:schemaLocation="
            http://www.ehcache.org/v3 
            http://www.ehcache.org/schema/ehcache-core-3.7.xsd">
  
    <persistence directory="demo/cache"/>
  
    <cache-template name="defaultTemplate">
        <expiry>
            <ttl unit="seconds">600</ttl>
        </expiry>

        <resources>
            <heap>100</heap>
            <offheap unit="MB">10</offheap>
            <disk persistent="true" unit="MB">20</disk>
        </resources>
    </cache-template>

    <cache alias="userCache" uses-template="defaultTemplate">
        <key-type>java.lang.Long</key-type>
        <value-type>com.example.User</value-type>
        <expiry>
            <ttl unit="seconds">1000</ttl>
        </expiry>
    </cache>

</config>
```

ehcache.xml에서 '캐시 템플릿'과 '캐시'를 설정할 수 있다. 캐시 템플릿(cache template)은 여러 캐시의 구성이 대체로 동일한 경우에 사용하면 좋다. 캐시 템플릿에는 캐시에 공통적으로 적용할 설정이 포함되어 개별 캐시의 설정보다 먼저 적용된다. 

- ```<persistence directory="">``` : 파일 기반 캐시를 저장할 디렉터리를 정의한다. 이 설정은 위치를 정의할 뿐이고 실제로 디스크 저장소에 저장하는 여부는 나중에 구성된다.
- ```<cache-template>``` : 캐시 템플릿을 정의한다.

- ```<cache alias="">``` : 캐시를 정의한다. 캐시의 이름은 alias로 지정한다.
  - ```<key-type>``` : 캐시 항목을 구분하는 key의 타입을 정의한다.
  - ```<value-type>``` : 캐시 항목 value의 타입을 정의한다.
  - ```<expiry>``` : TTL(Time to Live) 또는 TTI(Time to Idle)를 정의한다. TTL은 액세스와 관계없이 캐시 항목(entry)이 캐시에 남아 있을 수 있는 기간을 지정한다. TTL이 지나면 캐시에서 값이 제거된다. TTI는 액세스가 없을 때 캐시 항목이 캐시에 남아 있을 수 있는 기간을 지정한다. TTL, TTI의 기본 단위는 seconds다.
  - ```<resource>``` : 캐시의 메모리, 계층, 용량 등 자원을 정의한다. 아래의 heap, offheap, disk를 모두 설정하는 경우 3-tier 캐시가 된다. 기본 캐시 전략은 LRU(Least Recently Used)다. 캐시가 가득 찼을 때 다음 하위 계층으로(예를 들어 온힙 저장소에서 오프힙 저장소로) 캐시 항목을 보낸다. 
    - ```<heap>``` : 온힙 저장소를 정의한다. 저장 가능한 최대 항목 수를 지정한다.
    - ```<offheap>``` : 오프힙 저장소를 정의한다. 
    - ```<disk>``` : 디스크 캐시를 정의한다. 주의해야 할 것은 항상 힙 캐시보다 디스크 캐시의 메모리 용량이 커야 한다는 것이다. 그렇지 않으면 애플리케이션을 시작하는 중 XML 파일을 파싱할 때 예외가 발생한다.
    
## 3.2 application.yml
```yaml
spring:
  cache:
    jcache:
      config: classpath:ehcache.xml
```

Ehcache는 ehcache.xml 파일 혹은 자바 코드로 환경설정을 할 수 있다. 위처럼 xml 방식을 사용하는 경우, Spring이 Ehcache 설정 파일인 ehcache.xml을 찾을 수 있도록 속성을 추가한다.

## 4. 캐시 적용
### @Cacheable
```java
@Service
public class UserService {
    @Cacheable(cacheNames = "userCache")
    public UserDto getUser(Long userId) {...} 
}
```

## 5. 캐시 리스너
캐시 리스너 CacheEventListener를 이용해서 캐시의 이벤트에 따라 로그를 출력할 수 있다.

### ehcache.xml
```xml
<cache alias="userCache" uses-template="defaultTemplate">
    <key-type>java.lang.Long</key-type>
    <value-type>com.example.User</value-type>
    <expiry>
        <ttl unit="seconds">1000</ttl>
    </expiry>
    <listeners>
        <listener>
            <class>com.example.CacheLogger</class>
            <event-firing-mode>ASYNCHRONOUS</event-firing-mode>
            <event-ordering-mode>UNORDERED</event-ordering-mode>
            <events-to-fire-on>CREATED</events-to-fire-on>
            <events-to-fire-on>UPDATED</events-to-fire-on>
            <events-to-fire-on>EXPIRED</events-to-fire-on>
            <events-to-fire-on>REMOVED</events-to-fire-on>
            <events-to-fire-on>EVICTED</events-to-fire-on>
        </listener>
    </listeners>
</cache>
```

### CacheEventListener
```java
package com.example;

import org.ehcache.event.CacheEvent;
import org.ehcache.event.CacheEventListener;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class CacheLogger implements CacheEventListener<Object, Object> {
 
    private static final Logger log = LoggerFactory.getLogger(CustomCacheEventLogger.class);
 
    @Override
    public void onEvent(CacheEvent cacheEvent) {
        log.info("Cache event={}, Key={}, Old value={}, New value={}", 
                cacheEvent.getType(), cacheEvent.getKey(), cacheEvent.getOldValue(), cacheEvent.getNewValue());
    }
}
```

## 참고
https://medium.com/finda-tech/spring-%EB%A1%9C%EC%BB%AC-%EC%BA%90%EC%8B%9C-%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC-ehcache-4b5cba8697e0  
https://www.ehcache.org/  
https://www.ehcache.org/documentation/3.9/  
https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.caching  
https://springframework.guru/using-ehcache-3-in-spring-boot/  