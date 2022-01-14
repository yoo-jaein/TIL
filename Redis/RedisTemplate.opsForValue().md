# RedisTemplate.opsForValue()

## RedisTemplate
Spring에서는 Redis 캐시 도입을 쉽게 해주는 강력한 템플릿인 redisTemplate을 제공한다. redisTemplate을 이용하면 String, List, Set, Hash, Zset을 Redis에 저장할 수 있다. 

##

##

##

## increment()
- Time complexity: O(1)

```java
Long increment(K key)
```
- 키에 저장된 숫자를 1씩 증가시킨다. 키가 존재하지 않으면 작업을 수행하기 전에 0으로 설정된다.
- Redis에는 정수 유형이 없기 때문에 문자열 작업이다. 키에 저장된 문자열은 연산을 실행하기 위해 10진수 64비트 signed integer로 해석된다.
- 키에 잘못된 유형의 값이 포함되어 있거나 정수로 나타낼 수 없는 문자열이 포함되어 있으면 오류가 반환된다.

```java
Long increment(K key, long delta)
```
- 키에 저장된 숫자를 delta씩 증가시킨다.

```java
Double increment(K key, double delta)
```

## decrement()
- Time complexity: O(1)

```java
Long decrement(K key)
```

```java
Long decrement(K key, long delta)
```
- 키에 저장된 숫자를 delta씩 감소시킨다.

- 키에 저장된 숫자를 1씩 감소시킨다. 키가 존재하지 않으면 작업을 수행하기 전에 0으로 설정된다. 
- Redis에는 정수 유형이 없기 때문에 문자열 작업이다. 키에 저장된 문자열은 연산을 실행하기 위해 10진수 64비트 signed integer로 해석된다.
- 키에 잘못된 유형의 값이 포함되어 있거나 정수로 나타낼 수 없는 문자열이 포함되어 있으면 오류가 반환된다. 
- increment()와 함께 특정 데이터의 개수를 캐싱하는데 활용될 수 있다.

## 참고
https://docs.spring.io/spring-data/redis/docs/current/api/org/springframework/data/redis/core/ValueOperations.html  
https://redis.io/commands/INCR  