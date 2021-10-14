# Spring Data Redis

## Redis
Redis는 키-값 저장소이다. memcached와 비슷하지만 Redis의 데이터셋은 휘발성이 아니며, 값은 memcached와 똑같이 String일 수 있지만 List, Set, Hash 등이 될 수도 있다. 이 모든 데이터 유형은 푸시/팝 요소, 요소 추가/제거, 서버 측 조합 수행, 교차점, 세트 간의 차이 등에 대한 원자 작업으로 조작될 수 있다.

## Spring Data Redis 기능
- 여러 Redis 드라이버(Lettuce 및 Jedis)에 대한 저수준의 추상화 연결 패키지.

- Redis 드라이버 예외를 Spring의 예외 계층 구조로 예외 번역.

- 다양한 Redis 작업, 예외 번역 및 직렬화 지원을 수행하기 위한 높은 수준의 추상화를 제공하는 RedisTemplate 제공. 

- Pubsub 지원(예: 메시지 기반 POJO를 위한 MessageListenerContainer).

- Redis Sentinel과 Redis Cluster 지원.

- Lettuce 드라이버를 사용한 반응형 API.

- JDK, String, JSON 그리고 Spring Object/XML 매핑 serializers.

- Redis 위에 JDK Collection 구현.

- 아토믹 카운터 지원 클래스.

- 정렬 및 파이프라이닝 기능.

- SORT, SORT/GET 패턴 및 반환된 대량 값에 대한 전담 

- @EnableRedisRepositories를 사용한 사용자 지정 쿼리 방법 지원을 포함한 저장소 인터페이스의 자동 구현.

## Spring Data Redis를 사용하는 이유
Spring Data Redis(SDR) 프레임워크를 사용하면 Spring의 인프라 지원을 통해 Redis 저장소와 상호 작용하는 데 필요한 작업과 보일러플레이트 코드를 제거하여 Redis 키 값 저장소를 사용하는 Spring 응용 프로그램을 쉽게 작성할 수 있다.

## 참고
https://spring.io/projects/spring-data-redis  
