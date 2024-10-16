# Chunk-Oriented Processing
청크 지향 프로세싱, 청크 기반 스텝

## 청크 지향 프로세싱
청크 기반 스텝은 스프링 배치에서 가장 일반적으로 사용하는 스텝 유형으로, reader-processor-writer의 구조로 스텝을 처리한다. 지정한 크기(chunkSize, commit interval)에 도달할 때까지 아이템을 읽고 처리한 후, 청크 단위로 한 번에 트랜잭션을 커밋한다. 커밋 간격을 50개 아이템으로 설정했다면, 잡은 50개 아이템을 읽고 50개 아이템을 처리한 다음에 한 번에 50개 아이템을 기록한다.

## ItemReader-Processor-Writer
- ItemReader : 파일, 데이터베이스 등에서 데이터를 읽어오는 역할
- ItemProcessor : 데이터를 가공하거나 필터링하는 역할. Reader에서 읽은 타입을 변환하여 Writer로 전달하는 역할
- ItemWriter : ItemReader에서 읽어온 데이터를 파일이나 데이터베이스에 쓰는 역할

## 수도 코드
```java
List items = new Arraylist();
for(int i = 0; i < commitInterval; i++){
    Object item = itemReader.read();
    if (item != null) {
        items.add(item);
    }
}

List processedItems = new Arraylist();
for(Object item: items){
    Object processedItem = itemProcessor.process(item);
    if (processedItem != null) {
        processedItems.add(processedItem);
    }
}

itemWriter.write(processedItems);
```

## 장점
대량의 데이터를 여러 개의 청크로 나눠서 처리함으로써 스텝이 실패했을 때의 영향을 최소화할 수 있다. 예를 들어, 1000개의 데이터를 대상으로 배치를 실행한다고 가정해보자. Chunk-Oriented Processing을 적용하지 않는 경우 998번째 데이터에서 실패했을 때 998개의 데이터가 롤백된다. 그러나 청크의 단위를 10으로 하여 처리하면 8개의 데이터만 롤백되고 나머지 990개의 데이터는 영향을 받지 않는다. 이러한 이유로 스프링 배치에서는 Chunk-Oriented Processing을 지향한다.

## 청크 단위 설정하기
청크 단위가 1이라면 아이템을 하나씩 읽고 처리한 후 트랜잭션을 커밋한다. 이것이 기존의 아이템 기반 처리 방식이다. 하지만 이런 아이템 기반 방식을 사용하면 JobRepository의 잡의 상태가 커밋될 때마다 계속 갱신된다. 이 작업은 비용이 많이 들기 때문에 청크 단위를 어느 정도 크게 설정하는 것이 좋다.

## 주의!
Paging 기반의 ItemReader를 사용하는 경우 Page의 사이즈와 청크의 사이즈를 동일하게 설정하는 것이 좋다. Page 사이즈가 청크 단위보다 작은 경우, 한 번의 트랜잭션에서 여러 번의 쿼리가 발생하기 때문에 성능상 이슈가 발생할 수 있다. 또한, JPA를 사용하는 경우 영속성 컨텍스트가 깨지는 문제도 있다.  

## 참고
https://docs.spring.io/spring-batch/docs/current/reference/html/index-single.html#chunkOrientedProcessing  
스프링 배치 완벽 가이드 2/e  
https://jojoldu.tistory.com/331  