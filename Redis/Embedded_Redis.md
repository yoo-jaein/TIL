# Embedded Redis
테스트 코드를 위한 임베디드 레디스

## 도입 이유
레디스 repository에 대한 테스트 코드를 작성했다. 만약 테스트 코드에서 원격 레디스를 사용하게 되면 로컬에서는 당연히 잘 돌아가지만 ci/cd 파이프라인에서 원격에 붙을 수 없기 때문에 테스트가 전부 실패할 수 있다. 테스트 코드를 원활하게 실행시키기 위해 임베디드 레디스를 사용해야 한다.

## 1. embedded-redis 의존성 추가하기

- [ozimov embedded-redis](https://github.com/ozimov/embedded-redis)를 사용

### build.gradle

```groovy
implementation 'it.ozimov:embedded-redis:0.7.3'
```

## 2. application.yml

```yaml
# test/resources/application.yml
spring:
  redis:
    host: localhost
    port: 6379
```

## 3. 임베디드 레디스 설정 클래스 만들기

- RedisTemplate, Repository 등 설정은 생략
- https://jojoldu.tistory.com/297 의 EmbeddedRedisConfig 코드 참고

```java
@Slf4j
public class EmbeddedRedisConfig {

    @Value("${spring.redis.port}")
    private int redisPort;

    private RedisServer redisServer;

    @PostConstruct
    public void redisServer() throws Exception {
            redisServer = new RedisServer(redisPort);
            log.info("[TEST] Embedded Redis Start");
            redisServer.start();
    }

    @PreDestroy
    public void stopRedis() throws Exception {
        if (redisServer != null) {
            log.info("[TEST] Embedded Redis Stop");
            redisServer.stop();
        }
    }
}
```

## 4. 테스트 클래스에 Redis 설정 적용

```java
@Import(EmbeddedRedisConfig.class)
...
class TestClass {...}
```

## 5. 함께 보면 좋은 것
- @DataRedisTest
- @TestConfiguration
- 스프링 부트 2.4.0 이후 도입된 프로필 방식과 @ActiveProfiles
  - spring.config.activate.on-profile:
  - -Dspring.profiles.active=

## 참고
https://jojoldu.tistory.com/297  
