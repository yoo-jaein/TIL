# How to implement Redis repository
Redis 저장소를 구현하는 방법

## 1. RestTemplate
```java
@Repository
@RequiredArgsConstructor
public class RedisStringRepository {

    private RedisTemplate<String, Object> redisTemplate;

    public void save(String key, String value) {
        redisTemplate.opsForValue().set(key, value);
    }

    public String get(String key) {
        return (String) redisTemplate.opsForValue().get(key);
    }

    public void delete(String key) {
        redisTemplate.delete(key);
    }
}
```

- RedisTemplate의 opsForX 메서드를 통해 저장소를 직접 구현한다.

## 2. extends CrudRepository
```java
@RedisHash("users")
public class User {
    @Id private Long id;
    private String name;
    private int age;
}
```

- 대상 클래스에 @RedisHash를 명시한다.
- org.springframework.data.annotation의 @Id가 붙은 id 필드를 만든다.

```java
public interface RedisUserRepository extends CrudRepository<User, Long> {
}
```

- 사용하는 Redis의 Server version이 최소 2.8.0이어야 한다.
- 트랜잭션이 동작하지 않으므로 주의한다.
- Redis의 자료구조 중 Hash에만 적용할 수 있는 방법이다.
- 기본적인 CRUD와 검색 연산이 들어있는 CrudRepository를 상속받는다.
- 클라이언트에서는 아래 코드처럼 사용한다.

```java
RedisUserRepository redisUserRepository;
...
redisUserRepository.save(user);
redisUserRepository.findOne(id);
redisUserRepository.findAll();
redisUserRepository.count();
```

## 참고
https://docs.spring.io/spring-data/redis/docs/current/reference/html/#redis.repositories  
