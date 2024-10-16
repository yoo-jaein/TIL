# Redis data types
Redis 데이터 타입 알아보기

## Redis
Redis는 일반 key-value 저장소가 아니라 다양한 종류의 value를 지원하는 데이터 구조 서버다. 이것이 의미하는 바는 기존의 키-값 저장소는 문자열 key를 문자열 value에 연결하는 반면, Redis에서는 value가 단순한 문자열에 국한되지 않고 더 복잡한 데이터 구조도 보유할 수 있다는 의미이다.  

Redis에서 지원하는 데이터 타입에는 다음과 같은 것들이 있다.  

- Strings 
- Lists
- Sets
- Sorted Sets
- Hashes
- Bit arrays(특수 명령을 사용하여 개별 비트를 다룰 수 있는 타입)
- HyperLogLogs(집합의 카디널리티를 추정하는 데 사용되는 확률적 데이터 타입)

이 중 일반적으로 자주 사용되는 Strings, Lists, Sets, Sorted Sets, Hashes에 대해 알아보자.  

## Strings
- Redis String은 바이너리 세이프하다. 바이너리 세이프하다는 것은 모든 문자(바이트)로 구성될 수 있는 문자열이라는 뜻이다. 따라서 JPEG 이미지나 직렬화된 객체 등 모든 종류의 데이터가 저장될 수 있다.
- String 값의 최대 크기는 512MB다.

## Lists
- 삽입 순서에 따라 정렬되는 String 요소의 컬렉션이다.
- List의 Head(왼쪽) 또는 Tail(오른쪽)에 푸시할 수 있다.
- Redis List는 기본적으로 Linked List이다. 따라서, 수백만 개의 항목이 있을 때 Head, Tail에서 삽입/삭제 작업이 자주 일어나도 빠르게 처리할 수 있다. 다만 List의 중간에 접근하는 것은 느리다.
- List의 최대 길이는 2^32 - 1개(4,294,967,295개)다.

```text
LPUSH mylist a   # now the list is "a"
LPUSH mylist b   # now the list is "b","a"
RPUSH mylist c   # now the list is "b","a","c" (RPUSH was used this time)
```

- 이 작업 중 하나가 빈 key에 대해 수행되면 새로운 List가 생성된다. 마찬가지로 작업이 List를 비울 경우 key 공간에서 key가 제거된다.

## Sets
- 정렬되지 않고 고유한 String 요소의 컬렉션이다.
- O(1)에서 멤버를 추가, 제거 및 테스트하는 것이 가능하다.
- Set의 최대 길이는 2^32 - 1개(4,294,967,295개)이다.
- 다양한 명령을 지원하기 때문에 매우 짧은 시간에 Set의 합집합, 교집합, 차집합 연산을 수행할 수 있다.

## Sorted sets
- 모든 String 요소가 score라고 하는 부동 숫자 값과 연결된 정렬된 Set이다. 
- 항상 점수에 따라 정렬되기 때문에 Set과 달리 요소의 범위를 검색할 수 있다(예: 상위 10개 또는 하위 10개 가져오기).
- Sorted Set의 중간에 접근하는 것도 매우 빠르기 떄문에 대규모 랭킹이나 Redis 내부 데이터의 인덱싱 등에 활용될 수 있다.

## Hashes
- Hash는 값과 연결된 필드로 구성된 맵이다.
- 필드와 값은 모두 String이다.
- Hash는 최대 2^32 - 1개(4,294,967,295개)의 필드-값 쌍을 저장할 수 있다.
- 주로 객체를 나타내는 데 사용된다(예: 이름, 성, 나이 등과 같이 필드가 많은 User).

```text
HMSET user:1000 username antirez password P1pp0 age 34
HGETALL user:1000
HSET user:1000 password 12345
HGETALL user:1000
```

## 참고
https://redis.io/topics/data-types