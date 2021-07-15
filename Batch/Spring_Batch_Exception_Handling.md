# Spring Batch Exception Handling

## 재시작

## 스킵
프로세싱하는 동안 발생하는 에러 중에 단순히 스킵해도 되는 경우가 있다. 예를 들어, 은행 재무 데이터를 처리하다가 발생하는 에러는 스킵하면 안되지만, 회사 이름 데이터를 로드하는 과정에서 잘못된 형식으로 누락되는 에러는 상황에 따라 스킵해도 된다. 이렇듯 스킵 가능 여부는 그 데이터의 의미를 잘 이해하는 사람이 결정해야 한다.   

### 예시 1 : skip()
```java
@Bean
public Step step1() {
	return this.stepBuilderFactory.get("step1")
				.<String, String>chunk(10)
				.reader(flatFileItemReader())
				.writer(itemWriter())
				.faultTolerant()
				.skipLimit(10)
				.skip(FlatFileParseException.class)
				.build();
}
```
위의 코드에서는 FlatFileParseException이 발생하면 항목을 스킵하고 스킵에 대한 카운트를 한다. skipLimit 값인 10번째까지 스킵을 할 수 있으며, 11번째 스킵에서 Exception을 발생시키고 스텝이 실패하게 된다. 만약 FlatFileParseException이 아닌 다른 예외가 발생한다면 그대로 잡이 실패하게 된다.  

### 예시 2 : noSkip()
```java
@Bean
public Step step1() {
	return this.stepBuilderFactory.get("step1")
				.<String, String>chunk(10)
				.reader(flatFileItemReader())
				.writer(itemWriter())
				.faultTolerant()
				.skipLimit(10)
				.skip(Exception.class)
				.noSkip(FileNotFoundException.class)
				.build();
}
```
위의 코드에서는 java.lang.Exception을 스킵 가능한 클래스로, FileNotFoundException은 스킵할 수 없는 클래스로 설정했다. skip()과 noSkip() 메서드의 호출 순서는 상관 없다. 


## 재시도
대부분의 경우 예외에 대해 스킵 처리를 하거나 스텝 실패를 발생시켜서 해결한다. 하지만 개발 과정에서 모든 예외에 대한 처리를 명확하게 결정지을 수는 없다. 파일을 읽는 동안 발생할 수 있는 FlatFileParseException는 해당 레코드에 대해 몇 번을 시도하더라도 예외가 발생한다. 하지만 교착 상태에서 발생할 수 있는 DeadlockLoserDataAccessException이나 네트워크 글리치로 발생하는 예외는 기다렸다가 다시 시도하면 성공할 수도 있다.  

```java
@Bean
public Step step1() {
	return this.stepBuilderFactory.get("step1")
				.<String, String>chunk(2)
				.reader(itemReader())
				.writer(itemWriter())
				.faultTolerant()
				.retryLimit(3)
				.retry(DeadlockLoserDataAccessException.class)
				.build();
}
```
위의 코드에서는 DeadlockLoserDataAccessException이 발생하면 retryLimit 값인 3번까지 아이템을 재시도할 수 있다. 재시도의 작동 방법은 https://docs.spring.io/spring-batch/docs/4.3.x/reference/html/retry.html#retry 에 있다.

## 참고
https://docs.spring.io/spring-batch/docs/4.3.x/reference/html/step.html#stepRestart  