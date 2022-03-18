# Redis

## Redis
Redis는 인메모리 NoSQL Key-Value Database다. 메모리에 모든 데이터를 올려서 읽고 쓰기 때문에 속도가 빠르지만 용량에 한계가 있다. 그래서 메인 데이터베이스로 사용하기보다 캐시용으로 많이 사용된다. 또한, 스냅샷 백업(지정된 간격으로 데이터셋의 특정 시점의 스냅샷을 백업)과 파일 보관 방식(AOF, 서버에서 수신한 모든 쓰기 작업을 기록)을 지원한다.

### Single Thread
Redis는 싱글 스레드 모델을 사용한다. 그래서 keys와 같이 List를 full scan하는 명령어의 경우, 대량의 데이터가 들어있을 때 keys 명령어를 처리하기 위해 다른 명령이 지연되는 일이 발생할 수 있다.  

Redis가 싱글 스레드인 이유는 다음과 같다.
- Redis의 모든 작업은 메모리 기반이기 때문에 CPU는 Redis의 병목이 아니다. 
- Redis는 epoll과 이벤트 루프를 사용해서 컨텍스트 스위칭없이 성능을 뽑아낼 수 있다.
- 싱글 스레드 애플리케이션은 단일 CPU 코어가 있는 시스템에도 배포할 수 있다.

### Master-Replica
일반적으로 Redis는 성능 향상을 위해 Master-Replica 구조를 사용한다. Master 노드와 여러 개의 Replica 노드는 연결되어 있다. Master 노드에 변화가 발생하면 모든 Replica로 변화가 복제된다. 이 때 비동기 방식으로 통신한다. 일반적으로 Master는 write-only, Replica는 read-only로 구성하여 부하를 분산시킬 수 있다.  

한 개의 Master에 여러 개의 Replica가 붙을 수 있고, 한 개의 Replica에 다시 또 다른 Replica가 연결되는 것도 가능하다. 하지만 한 개의 복제 그룹에서는 항상 한 개의 Master 노드만 존재한다.  

### Sentinel
만약 운영 중에 Master에 문제가 발생해서 더이상 통신되지 않는다면 어떻게 될까? Redis의 Sentinel은 Master-Replica를 감시하고 있다가 Master가 다운되면 이를 감지해서 자동으로 Replica 중 하나를 Master로 승격시킨다. Sentinel은 Master 장애 조치 외에도 지속적인 모니터링, 알림 등의 기능을 제공한다. Sentinel은 3개 이상의 인스턴스로로 구성할 것을 권고한다. 3개 이상의 홀수 인스턴스를 띄워서 과반수 이상의 허가를 받았을 때만 Master 장애 판단 및 승격이 이루어지게 해야 한다. 이 때 과반수를 quorum(쿼럼)이라고 부른다. 3개의 Sentinel이 있다면 quorum은 2이다.

### Sentinel-Failover
하나의 Master에 두 개의 Replica가 연결되어 있다고 해보자. 
1. Master가 다운되면 이를 감시하고 있던 3개의 Sentinel들은 Master가 진짜로 다운되었는지 투표를 진행한다.
2. 과반수 이상이 동의하면 Failover(페일 오버, 장애 조치)를 시작한다.
3. Master에 대한 복제 연결을 끊고, Replica 노드 중 하나를 선택하여 Master로 승격시킨다.
4. 또 다른 Replica 노드는 승격된 Master 노드에 연결시킨다. 만약 다운되었던 노드(기존 Master)가 다시 살아난다면 승격된 Master에 Replica로 연결된다.

### Cluster
Redis가 Cluster(클러스터)로 구성되었다는 것은 모든 노드가 서로서로 연결된 Full Mesh 구조라는 뜻이다. Cluster 구조에서는 최소 3개 이상의 Master 노드가 필요하며, 모든 Master와 Replica는 서로 연결되어 있다. 이들의 통신은 gossip protocol(가십 프로토콜)을 이용한다. 가십 프로토콜은 클러스터에 있는 노드 간에 정보를 공유하는 프로토콜이다. 블록체인 네트워크와 분산 데이터베이스 환경에서 일관성을 달성하기 위해 사용된다. 가십은 어떤 정보를 다수의 노드로부터 인증받아서 하나의 합의를 이끌어낸다.  

### Sharding
Redis Cluster로 들어오는 모든 데이터는 16384개의 해시 슬롯에 분산 저장된다. 16384개의 슬롯은 Master 노드에 나뉜다. 예를 들어 Cluster의 Master가 3개라면 아래처럼 슬롯이 분배될 수 있다.
- 노드 A는 0부터 5500까지 해시 슬롯
- 노드 B는 5501부터 11000까지 해시 슬롯
- 노드 C는 10001부터 16383까지 해시 슬롯

키의 해시 슬롯이 무엇인지 알아내려면 key modulo 16384의 CRC16을 사용하면 된다. 해시 슬롯은 Master 노드 사이에서 자유롭게 옮겨질 수 있기 때문에 Cluster 구조는 확장성이 좋다. 주의해야할 점은 Redis의 트랜잭션은 동일한 해시 슬롯 내에서만 발생한다는 것이다. 따라서 트랜잭션을 수행하는데 관련된 모든 키가 동일한 슬롯에 있는지 확인해야 한다. 

### Cluster-Failover
#### 시나리오 1
Master 노드 3개, Replica 노드 3개인 상황에서 어떤 Master 하나가 다운되었다. Replica 노드 중 하나가 Master 노드로 승격된다. 만약 다운되었던 노드가 다시 복구되면 이는 Replica 노드가 된다.

#### 시나리오 2
Master 노드 3개, Replica 노드 3개인 상황에서 아무 Master에 Replica 노드를 하나 더 연결시킨다. Cluster 구조에서는 Replica 노드 중 하나가 다운되어 Replica 노드가 없는 Master가 생기면, Replica 노드가 두 개인 Master의 Replica 노드 하나를 가져와 그 자리에 채울 수 있다. 이때 모든 과정은 사용자의 개입 없이 클러스터 내부 통신으로 진행된다.

### Sentinel vs Cluster
Sentinel 구조는 Sentinel이 노드들을 감시하지만 Redis Cluster 구조는 모든 노드가 서로서로 감시한다는 점이 다르다. 하지만 둘 다 Failover가 발생하는 경우, 쓰기가 허용되지 않는 짧은 시간이 발생하므로 애플리케이션이 이를 정상적으로 처리할 수 있는지 확인해야 한다.

## 참고

https://redis.io/topics/sentinel  
https://meetup.toast.com/posts/226  
https://medium.com/@amila922/redis-sentinel-high-availability-everything-you-need-to-know-from-dev-to-prod-complete-guide-deb198e70ea6#:~:text=Redis%20sentinel%20is%20the%20high,mode%20without%20any%20human%20intervention.