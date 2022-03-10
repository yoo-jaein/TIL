# Redis

## Redis
Redis는 인메모리 NoSQL Key-Value Database다. 메모리에 모든 데이터를 올려서 읽고 쓰기 때문에 속도가 빠르지만 용량에 한계가 있다. 그래서 메인 데이터베이스로 사용하기보다 캐시용으로 많이 사용된다. 또한, 스냅샷 백업과 파일 보관 방식으로 백업 기능을 지원한다.  

일반적으로 Redis는 성능 향상을 위해 Master-Replica 클러스터링 구조를 사용한다. Master 노드와 여러 개의 Replica 노드는 연결되어 있다. Master 노드에 변화가 발생하면 모든 Replica로 변화가 복제된다. 이 때 비동기 방식으로 통신한다. 일반적으로 Master는 write-only, Replica는 read-only로 구성하여 부하를 분산시킬 수 있다.  

만약 운영 중에 Master에 문제가 발생해서 더이상 통신되지 않는다면 어떻게 될까? Redis의 Sentinel은 Master-Replica를 감시하고 있다가 Master가 다운되면 이를 감지해서 자동으로 Replica 중 하나를 Master로 승격시킨다. Sentinel은 Master 장애 조치 외에도 지속적인 모니터링, 알림 등의 기능을 제공한다. Sentinel은 3개 이상의 인스턴스로로 구성할 것을 권고한다. 3개 이상의 홀수 인스턴스를 띄워서 과반수 이상의 허가를 받았을 때만 Master 장애 판단 및 승격이 이루어지게 해야 한다.

## 참고
https://zangzangs.tistory.com/72  
https://redis.io/topics/sentinel  