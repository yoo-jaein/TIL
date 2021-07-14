# Chunksize

```java
@Slf4j
@RequiredArgsConstructor
@Configuration
public class StudentJobConfiguration {

	private final JobBuilderFactory jobBuilderFactory;
	private final StepBuilderFactory stepBuilderFactory;
	private final DataSource dataSource;

	private static final int chunkSize = 10;
    //...
```

일반적으로 커밋 간격(commit interval)을 하드 코딩해 청크 크기를 정의하지만 이렇게 하는 것이 모든 상황에 적절한 것은 아니다. 예를 들어 계좌 하나의 모든 거래 내역을 단일 트랜잭션으로 처리해야 하는 상황이 있을 수 있다. 스프링 배치는 CompletionPolicy 인터페이스의 구현체를 제공함으로써 청크가 완료되는 시점을 프로그래밍 방식으로 정의할 수 있게 해준다.

## CompletionPolicy
CompletionPolicy 인터페이스는 청크의 완료 여부를 결정할 수 있는 로직을 구현할 수 있게 해준다. 기본적으로 SimpleCompletionPolicy 구현체를 사용한다. 이 클래스는 처리된 아이템 개수를 세는데, 이 개수가 미리 구성해둔 임곗값에 도달하면 청크 완료로 표시한다.

## TimeoutTerminationPolicy
또 다른 구현체는 TimeoutTerminationPolicy이다. 이 구현체를 사용해 타임아웃 값을 구성하면, 청크 내에서 처리 시간이 타임아웃 값을 넘을 때 안전하게 빠져나갈 수 있다. 안전하게 빠져나간다는 것은 해당 청크가 완료된 것으로 간주되고, 모든 트랜잭션 처리가 정상적으로 계속됨을 의미한다.

## CompositeCompletionPolicy
CompositeCompletionPolicy를 사용하면 여러 정책을 함께 구성할 수 있다. 

## 참고
스프링 배치 완벽 가이드 2/e  