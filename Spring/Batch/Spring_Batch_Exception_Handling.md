# Spring Batch Exception Handling
Restart, Skip, Retry

## 잡 실패에 따른 중지
잡이 중지되면 현재 작업 중인 청크를 롤백한다. 스프링 배치는 예외가 발생하면 기본적으로 스텝 및 잡이 실패한 것으로 간주한다. 잡을 중지하는 방식에는 StepExecution을 사용하는 것과 예외를 사용하는 것이 있다. 이 두 가지 방식의 큰 차이는 잡의 상태다. StepExecution은 ExitStatus.STOPPED 상태로 스텝이 완료된 후 잡이 중지되고, 예외가 발생한 경우는 스텝이 완료되지 않은채 통과되며 스텝과 잡에 ExitStatus.FAILED 상태가 남게 된다. 스텝이 FAILED로 식별되면 재시작할 때 중단된 청크의 아이템에서 다시 시작하게 된다.  

## 재시작
재시작을 할지 말지를 결정하는 것은 사용자의 몫이다. 기본적으로 잡의 재시작이 가능하도록 구성된다. 그러나 preventRestart() 메서드를 잡에 설정해주면 잡이 실패하거나 어떤 이유로든 중지된 경우에 다시 실행할 수 없다.

### 예시 1 : preventRestart()
```java
@Bean
public Job transactionJob() {
    return this.jobBuilderFactory.get("transactionJob")
        .preventRestart()
        .start(importTransactionFileStep())
        .next(applyTransactionStep())
        .next(generateAccountSummaryStep())
        .build();
}
```
잡을 다시 실행하려고 시도하면 JobRestartException이 발생한다.

### 예시 2 : startLimit()
```java
@Bean
public Step importTransactionFileStep() {
    return this.stepBuilderFactory.get("importTransactionFileStep")
        .startLimit(2)
        .<Transaction, Transaction>chunk(100)
        .reader(transactionReader())
        .writer(transactionWriter(null))
        .allowStartIfComplete(true)
        .listener(transactionReader())
        .build();
}
```
스텝 수준에서 재시작 횟수를 설정할 수도 있다. startLimit이 2로 구성되어 있으므로 이 잡은 두 번까지만 실행 가능하다. 첫 실행은 한 번의 시도이므로 이후에는 한 번의 시도만을 더 허용한다. 잡을 그 이상으로 실행하려고 시도하면 StartLimitExceedException이 발생한다. 

## 스킵
스프링 배치는 특정 예외가 발생했을 때 아이템을 건너뛰는 기능을 제공한다. 작업 도중에 발생하는 예외 중에 단순히 스킵해도 되는 경우가 있다. 예를 들어, 은행 재무 데이터를 처리하다가 발생하는 에러는 스킵하면 안되지만, 회사 이름 데이터를 로드하는 과정에서 잘못된 형식으로 누락되는 에러는 상황에 따라 스킵해도 된다. 이렇듯 스킵 가능 여부는 그 데이터의 의미를 잘 이해하는 사람이 결정해야 한다.   

### 예시 1 : skip(), skipLimit()
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

### ItemWriter에서의 Skip
ItemWriter는 write할 item들의 리스트(list)를 수신한다. 만약 스킵 가능한 예외가 발생하면 스프링 배치에서는 실제로 예외를 발생시킨 아이템을 확인하여 해당 항목만 건너뛴다. 이 방법은 트랜잭션을 롤백하고 커밋 간격을 1로 변경한 다음 각 항목을 재처리하고 다시 쓰기를 시도하는 것이다. 이렇게 하면 전체 청크를 건너뛸 필요 없이 오류가 있는 아이템만 건너뛸 수 있다.  

### 스킵과 로그 처리
스킵 기법을 사용하면 향후 원인 분석을 할 수 있도록 추가적인 작업이 필요할 수도 있다. 이럴 때는 ItemListener/ItemListenerSupport/SkipListener/@OnReadError 등을 적절히 구현하여 문제점을 확인할 수 있는 충분한 정보를 로그로 기록해야 한다.


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
스프링 배치 완벽 가이드 2/e  
https://stackoverflow.com/questions/22522681/spring-batch-skip-exception-for-itemwriter  