# RedisTemplate.opsForValue()

## RedisTemplate
- Spring에서는 Redis 캐시 도입을 쉽게 해주는 강력한 템플릿인 redisTemplate을 제공한다. redisTemplate을 이용하면 String, List, Set, Hash, Zset을 Redis에 저장할 수 있다.   

## RedisTemplate의 opsFor*
RedisTemplate의 opsFor* 메소드들은 특정 컬렉션의 커맨드(Operation)을 호출할 수 있는 기능을 모아둔 *Operations 인터페이스를 반환한다.

- **opsForValue() : ValueOperations : Redis 자료구조 String**  
- opsForList() : ListOperations : Redis 자료구조 List  
- opsForSet() : SetOperations : Redis 자료구조 Set  
- opsForZSet() : ZSetOperations : Redis 자료구조 Sorted Set  
- opsForHash() : HashOperations : Redis 자료구조 Hash  

## RedisTemplate.opsForValue()
String 값에 대한 Redis 작업이다.

## set()
- Time complexity: O(1)

```java
void set(K key, V value);
```
- 키의 값을 설정한다.
- 키에 이미 값이 있으면 유형에 관계없이 덮어쓴다.

```java
void set(K key, V value, long timeout, TimeUnit unit);
```

```java
default void set(K key, V value, Duration timeout)
```
- 키에 대한 만료 타임아웃을 함께 설정한다.
- 키와 관련된 이전 TTL은 SET 작업이 성공하면 삭제된다.

## setIfAbsent()
- Time complexity: O(1)

```java
Boolean setIfAbsent(K key, V value);
```
- 키가 존재하지 않는 경우 문자열 값을 유지하도록 키를 설정한다. 이 경우 SET와 동일하다. 
- 키에 이미 값이 있으면 작업이 수행되지 않는다.

```java
Boolean setIfAbsent(K key, V value, long timeout, TimeUnit unit);
```

```java
default Boolean setIfAbsent(K key, V value, Duration timeout)
```

## setIfPresent()
- Time complexity: O(1)

```java
Boolean setIfPresent(K key, V value);
```
- 키가 존재하는 경우 문자열 값을 유지하도록 키를 설정한다.

```java
Boolean setIfPresent(K key, V value, long timeout, TimeUnit unit);
```

```java
default Boolean setIfPresent(K key, V value, Duration timeout)
```

## multiSet()
- Time complexity: O(N), 설정할 키의 개수가 N일 때

```java
void multiSet(Map<? extends K, ? extends V> map)
```
- 튜플에서 제공되는 키-값 쌍을 사용하여 여러 키를 여러 값으로 설정한다.
- 주어진 키를 각각의 값으로 설정한다. 일반 SET과 마찬가지로 기존 값을 새 값으로 바꾼다. 
- 원자적이므로 주어진 모든 키가 한 번에 설정된다.

## multiSetIfAbsent()
- Time complexity: O(N), 설정할 키의 개수가 N일 때

```java
Boolean multiSetIfAbsent(Map<? extends K, ? extends V> map);
```
- 제공된 키가 존재하지 않는 경우에만 튜플에 제공된 키-값 쌍을 사용하여 여러 키를 여러 값으로 설정한다.
- 주어진 키를 각각의 값으로 설정한다. MSETNX는 키 하나가 이미 존재하는 경우에 모든 작업을 수행하지 않는다.
- 결과는 모든 키가 설정되었을 때 / 모든 키가 설정되지 않았을 때(최소 한 개 이상의 키가 이미 존재)로 구분된다.
- 원자적이므로 주어진 모든 키가 한 번에 설정된다.

## get()
- Time complexity: O(1)

```java
V get(Object key);
```
- 키 값을 가져온다. 
- GET은 Redis String 값만 처리하므로 키에 저장된 값이 String이 아닌 경우 오류가 반환된다.

## getAndDelete()
- Time complexity: O(1)

```java
V getAndDelete(K key);
```
- 키 값을 가져오고 성공 시 키를 삭제한다.

## getAndExpire()
- Time complexity: O(1)

```java
V getAndExpire(K key, long timeout, TimeUnit unit);
```
- 키 값을 가져오고 타임아웃을 적용시킨다.

```java
V getAndExpire(K key, Duration timeout);
```

## getAndPersist()
- Time complexity: O(1)

```java
V getAndPersist(K key);
```
- 키의 값을 반환하고 키를 유지합니다. 
- 이 작업은 키와 연결된 모든 TTL을 제거한다.

## getAndSet()
- Time complexity: O(1)

```java
V getAndSet(K key, V value);
```

> Deprecated Notice: Redis 버전 6.2.0부터 이 명령은 더 이상 사용되지 않는 것으로 간주된다. 완전히 제거될 가능성은 낮지만 대신 `!GET` 인수와 함께 `SET`을 사용하는 것이 좋다.

- 원자적으로 동작한다.
- 키에 값을 설정하고 키에 저장되었던 이전 값을 반환한다. 
- 키가 존재하지만 String 값을 보유하지 않는 경우 오류를 반환한다. 
- 이전에 설정된 TTL은 SET 작업이 성공하면 초기화된다.

## multiGet()
- Time complexity: O(N), 검색할 키의 개수가 N일 때

```java
List<V> multiGet(Collection<K> keys);
```
- 여러 키를 가져온다. 
- 값은 요청된 키의 순서로 반환된다.

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
- 키에 저장된 숫자를 1씩 감소시킨다. 키가 존재하지 않으면 작업을 수행하기 전에 0으로 설정된다.
- Redis에는 정수 유형이 없기 때문에 문자열 작업이다. 키에 저장된 문자열은 연산을 실행하기 위해 10진수 64비트 signed integer로 해석된다.
- 키에 잘못된 유형의 값이 포함되어 있거나 정수로 나타낼 수 없는 문자열이 포함되어 있으면 오류가 반환된다.
- increment()와 함께 특정 데이터의 개수를 캐싱하는데 활용될 수 있다.

```java
Long decrement(K key, long delta)
```
- 키에 저장된 숫자를 delta씩 감소시킨다.

## append()
- Time complexity: O(1). Redis에서 사용하는 동적 문자열 라이브러리는 모든 재할당에서 사용 가능한 여유 공간을 두 배로 늘린다. 때문에 추가되는 값이 작고 이미 존재하는 값이 임의의 크기라고 가정할 때 대략 O(1)이 된다.

```java
Integer append(K key, String value);
```
- 키가 이미 존재하고 String인 경우 String 끝에 값을 추가한다. 
- 키가 존재하지 않으면 생성되어 빈 문자열로 설정되므로 APPEND는 이 경우 SET와 유사하다.

## get()
- Time complexity: O(N), 반환된 문자열 길이가 N일 때. 작은 문자열의 경우 O(1)로 간주할 수 있다. 

```java
String get(K key, long start, long end);
```
- 키 값의 start와 end 사이 Sub-String을 가져온다. start와 end를 모두 포함시킨다.
- 음수 오프셋을 사용하여 문자열 끝에서 시작하는 오프셋을 적용할 수 있다. -1은 마지막 문제, -2는 끝에서 두 번째 문자 등을 의미한다.
- 결과 범위는 문자열의 실제 길이로 제한한다.

## set() 
- Time complexity: O(1). 일반적으로 인수 문자열은 매우 작기 때문에 대략 O(1)이 된다. 그렇지 않다면 복잡도는 인수의 길이가 M일 때 O(M)이다.

```java
void set(K key, V value, long offset);
```
- 지정된 오프셋에서 시작하여 키에 저장된 문자열 값의 일부를 덮어쓴다.
- 오프셋이 문자열 값의 원래 길이보다 큰 경우, 문자열은 오프셋을 맞추기 위해 0바이트로 채워진다.
- Redis String은 512MB로 제한된다. 따라서 설정할 수 있는 최대 오프셋은 (2^29)-1이다. 이 크기 이상으로 확장해야 하는 경우 여러 개의 키를 사용할 수 있다.

## size()
- Time complexity: O(1)

```java
Long size(K key);
```
- 키에 저장된 문자열 값의 길이를 가져온다.
- 키에 문자열이 아닌 값이 있으면 오류가 반환된다.

## setBit()
```java
Boolean setBit(K key, long offset, boolean value);
```

## getBit()
```java
Boolean getBit(K key, long offset);
```

## bitField()
```java
List<Long> bitField(K key, BitFieldSubCommands subCommands);
```

## 참고
https://docs.spring.io/spring-data/redis/docs/current/api/org/springframework/data/redis/core/ValueOperations.html  
https://redis.io/commands/INCR  