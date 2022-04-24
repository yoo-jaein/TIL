# High Performance Batch Processing
고성능 일괄 처리

## 스프링 배치의 고성능 처리 기법
### 1. 하나의 스텝을 처리할 때 Chunk를 병렬로 실행하기
하나의 스텝을 처리할 때 Chunk단위로 병렬로 실행하는 방법이다. thread-safe한 Reader와 Writer를 사용해야 한다. thread-safe하지 않은 Reader를 사용하고 싶다면 SynchronizedItemStreamReader로 래핑해야 한다.  

스레드 풀의 크기를 설정할 수 있다. 그러나 어떤 스레드에서 어떤 데이터를 처리할지 세밀한 조정이 불가능하다. 주의해야 할 것은 멀티스레드로 각 Chunk가 개별로 실행되기 때문에 실패 지점에서 재시작할 수 없다는 것이다. 멀티스레드의 경우 여러 개의 Chunk가 동시에 실행되다보니 10번째 Chunk가 실패했다고 해서 이전의 Chunk가 모두 성공된 상태임이 보장되지 않는다. 그래서 ItemReader의 saveState 옵션을 false로 설정한다. 이 방법은 하나의 스텝을 처리하기 때문에 StepExecution이 1개 발생한다.

### 2. 여러 스텝을 처리할 때 병렬로 실행하기
여러 개의 스텝을 Flow로 묶어 병렬로 실행한다. 하나의 스텝에 대해 성능이 향상되지는 않는다. 

### 3. 비동기 Processor, Writer 사용하기 
스텝에서 데이터를 처리하는 시간이나 쓰는 시간이 오래 걸리는 경우 사용할 수 있다. Processor와 Writer를 비동기 버전으로 사용한다. 

### 4. Remote Chunking: 스텝의 처리를 분산 실행하기
스텝의 처리(Reader-Processor-Writer)를 분산 실행한다. 아이템을 읽는 Master 노드가 있고, 아이템을 처리하고 쓰는 Worker 노드가 있다. Master 노드에서 ItemReader로 아이템을 읽은 다음 ChunkProvider를 통해 별도의 미들웨어(일반적으로 데이터가 사라지지 않도록 메세지 큐를 둔다)로 보낸다. 여러 Worker 노드에서 메세지 큐에서 데이터를 꺼내서 처리하고 쓴다. 정상적으로 처리가 완료되면 Master 노드에게 알려준다.

### 5. Partitioning: 파티셔닝하기
데이터를 나눠서 분산 실행한다. Manager (Master) 스텝에서 데이터를 작은 파티션으로 나눈다. 여러 Worker 스텝에서 각 파티션이 병렬로 처리된다. Worker 스텝은 동일한 환경(로컬), 별도의 환경(원격) 모두 지원한다. Master와 Worker 모두 별도의 완전한 스텝이기 때문에 개별적인 StepExecution을 가진다.  

Manager 스텝에는 Partitioner와 PartitionHandler가 사용된다.  
- Partitioner : 몇 개의 Worker 스텝(StepExecution)을 가질지 설정한다. partition(int gridSize) 메서드를 사용한다.
- PartitionHandler : Manager 스텝이 Worker 스텝을 어떻게 다룰지 정의한다. 구현체로 TaskExecutorPartitionHandler(단일 JVM 내에서 파티셔닝), MessageChannelPartitionHandler(원격 파티셔닝) 등이 있다.

하나의 스텝을 Chunk 단위 병렬로 처리하는 것과 차이가 있다. 파티셔닝을 하면 원격으로 분산 처리가 가능하고, 조회 조건이 1일~30일 일때 Worker 스텝마다 하루 단위로 분할 처리를 할 수도 있다. 단일 스텝을 멀티스레드로 처리하는 방법보다 다양한 조건으로 일감을 분할 처리할 수 있다. 데이터가 Master에서 이미 나눠지기 때문에 로컬에서 파티셔닝하는 경우에도 Reader, Writer의 thread-safe 여부가 중요하지 않다.

## 참고
https://www.youtube.com/watch?v=J6IPlfm7N6w  
https://jojoldu.tistory.com/493  
https://jojoldu.tistory.com/550  
