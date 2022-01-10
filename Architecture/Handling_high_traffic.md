# Handling high traffic
대용량 트래픽 처리하기. 분당 1억 건의 Post 리퀘스트를 처리하는 방법은?

## 관련 키워드
- 고가용성 high availability
- 수평 확장, 스케일아웃 scale out, 
- 분산 시스템 distributed system
- 페일오버 fail over
- 반정규화
- 샤딩 sharding
- Grokking the system design interview (educative.io)
- 데이타 중심 애플리케이션 설계 (책)
- 자바 jsr133, concurrent 패키지, 스레드 핸들링, 스레드 풀링
- 시스템 레이어
- CQRS
- 이벤트 소싱 event sourcing
- jmeter

## 생각 키워드
- 대용량 트래픽을 유연하게 처리할 수 있는 아키텍처는 무엇인가?
- 그 아키텍처는 왜 그렇게 구성되었는가?
- 내가 애플리케이션을 만들 때 얼마나 잘 설계하고 효율적으로 동작하게 만들었는가?

## 대용량 처리시 발생 가능한 이슈와 적절한 대응책
- DB 커넥션풀 문제 - 중간 캐시나 api 서버 두기. chain slave 쓰기
- 데이터는 단순함. RDB로는 쓰기 작업을 감당할 수 없음 - MongoDB
- MongoDB도 부족함 - Hbase
- DB보다 빠르고 단순하게 - Redis
- 파일이 너무 큼 - Hadoop
- 로그가 너무 많이 쌓이고 찾아보기 힘듬 - EFK 스택
- WAS를 scalable하게 늘려야 함 - kubernetes
- 메시지가 많이 들어오는데 이벤트를 하나도 놓치면 안됨 - Kafka
- 데이터가 많고 처리할게 많음 - spark
- 중복 방지 - Redis, db 락, jpa 락 제어, try and cancel

- DB 트랜잭션 - 기본적으로 DB 트랜잭션은 짧게. DB 질의는 느리기 때문에 병목의 원인이 됨. 대용량 트래픽의 경우 master-slave 구조로 read를 분산해주고, 더 많은 트래픽은 master를 sharding하거나 트랜잭션을 비동기화하는 방법.
- 많은 읽기 작업 - 적절한 캐시 전략.
- DB 캐시 - DB와 동일한 데이터를 들고 있는 캐시 서버에서 사용자의 read를 주로 처리. 변경되는 데이터가 있으면 DB에 write하고 캐시를 갱신. 캐시를 서버 메모리에 둘수도 있지만 서버가 죽으면 다 날라가니까 따로 캐시서버(주로 Redis cluster)를 구성. session sticky 참고.
- DB 검색 최적화 - DB 데이터가 많을 때 검색을 빨리 하려면 인덱싱 해야 함. 그런데 인덱스를 걸면 write할 때 정렬을 해야하기 때문에 write가 느려짐. 그래서 write하는 노드를 따로 두고 이 노드와 동기화하는 read replica (읽기 노드)를 여러 대 두는 식으로 많이 구성함. 
- RDBMS - RDB는 스케일아웃이 어려움. 스케일아웃이 용이해야하고 스키마가 유동적이고 write가 빈번한 서비스라면 nosql 도입을 고려. 하지만 nosql이 RDB를 대체하지는 못 함.

- 데이터 오퍼레이션 분리 - CQRS 또는 이벤트 소싱 도입.
- msa에서 분산 트랜잭션 - saga 참고

## 자료
- https://youtu.be/-W9F__D3oY4
- https://www.popit.kr/cqrs-eventsourcing/
- https://aws.amazon.com/ko/blogs/korea/prime-day-2021-two-chart-topping-days/
- https://aws.amazon.com/ko/blogs/korea/5-years-scalling-up-to-10-million-users/