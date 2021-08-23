# List of ItemReaders

## AbstractItemCountingItemStreamItemReader
ItemReader에서 반환된 아이템의 수를 계산하여 기본적인 재시작 기능을 제공하는 추상 기본 클래스다.

## AggregateItemReader

## AmqpItemReader
메세징 시스템을 위한 ItemReader. 스프링 AmqpTemplate이 주어지면 동기식 수신 메서드를 제공한다. receiveAndConvert() 메서드를 사용하면 POJO 객체를 받을 수 있다.

## KafkaItemReader
아파치 카프카 토픽에서 메시지를 읽는 ItemReader. 동일한 토픽 한 개에서 다수의 파티션의 메시지를 읽도록 설정할 수 있다. 또한, 재시작을 기능을 지원하기 위해 메시지의 오프셋을 execution context에 저장한다. 

## FlatFileItemReader
플랫 파일에서 읽는 ItemReader.

## HibernateCursorItemReader
HQL 쿼리에서 커서 기반으로 읽는 ItemReader.

## HibernatePagingItemReader
HQL 쿼리에서 페이징 처리를 하며 읽는 ItemReader.

## ItemReaderAdapter

## JdbcCursorItemReader
JDBC를 통해 데이터베이스 커서로부터 읽는 커서 기반의 ItemReader.

## JdbcPagingItemReader
SQL문이 주어지면 메모리 부족 없이 대용량 데이터 셋을 읽을 수 있도록 행을 통해 페이징 처리를 하는 ItemReader.

## JmsItemReader

## JpaPagingItemReader
JPQL문이 주어지면 메모리 부족 없이 대용량 데이터 셋을 읽을 수 있도록 행을 통해 페이징 처리를 하는 ItemReader.

## ListItemReader

## MongoItemReader

## Neo4jItemReader

## RepositoryItemReader

## StoredProcedureItemReader

## StaxEventItemReader

## JsonItemReader

## 참고
https://docs.spring.io/spring-batch/docs/current/reference/html/index-single.html#listOfReadersAndWriters  