# How to measure execution time for a Java code
자바 코드의 실행시간 측정하기

## 코드 실행시간 측정하기
자바 코드의 실행시간을 측정하는 도구에는 다음과 같은 것들이 있다.

- System.currentTimeMillis()
- System.nanoTime()
- StopWatch
  - springframework : [org.springframework.util.StopWatch](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/util/StopWatch.html) 
  - apache : [org.apache.commons.lang3.time.StopWatch](https://commons.apache.org/proper/commons-lang/apidocs/org/apache/commons/lang3/time/StopWatch.html)
  - guava : [com.google.common.base.Stopwatch](https://guava.dev/releases/19.0/api/docs/com/google/common/base/Stopwatch.html)

## 1. System.currentTimeMillis
```java
long start = System.currentTimeMillis();
// 실행 시간을 측정할 코드
// ...
long stop = System.currentTimeMillis();
System.out.println(stop - start);
```
```text
16
```

## 2. System.nanoTime
```java
long start = System.nanoTime();
// 실행 시간을 측정할 코드
// ...
long stop = System.nanoTime();
System.out.println(stop - start);
```
```text
9565875
```
- currentTimeMillis()보다 정확한 측정이 가능하다.

## 3. StopWatch
라이브러리별로 다양한 StopWatch가 존재한다.

### 3-1. springframework

- 스레드에 안전하지 않으며 동기화를 사용하지 않는다.
- 프로덕션 코드용이 아니라 개발 중이나 테스트에서 성능을 확인하는 데 사용된다.
  ```java
  // StopWatch#start
  public void start(String taskName) throws IllegalStateException {
      if (this.currentTaskName != null) {
          throw new IllegalStateException("Can't start StopWatch: it's already running");
      }
      this.currentTaskName = taskName;
      this.startTimeNanos = System.nanoTime();
  }
  ```
- Spring 5.2부터 내부적으로 System.nanoTime()을 사용한다. 코드 가독성을 높이고 계산 오류 가능성을 줄이는 목적으로 만들어졌다.
  ```java
  List<String> list = new ArrayList<>();
  for (int i = 0; i < 300000; i++) {
      list.add(String.valueOf(i));
  }
  
  StopWatch stopWatch = new StopWatch("partition");
  stopWatch.start(); // 측정 시작
  
  // 실행 시간을 측정할 코드
  List<List<String>> resultBy4 = partition(list, 4); 
  
  stopWatch.stop(); // 측정 종료
  System.out.println(stopWatch.prettyPrint());
  ```
  ```text
  StopWatch 'partition': running time = 11161250 ns
  ---------------------------------------------
  ns         %     Task name
  ---------------------------------------------
  011161250  100%
  ```
- prettyPrint()를 통해 측정 결과를 사람이 읽기 좋게 출력할 수 있다.

### 3-2. apache

- 스레드에 안전하지 않다.
  ```java
  StopWatch stopWatch = new StopWatch();
  stopWatch.start(); // start() 혹은 createStarted()를 호출
  // 실행 시간을 측정할 코드
  // ...
  stopWatch.stop();
  System.out.println(stopWatch.getTime());
  ```
  ```text
  1
  ```

### 3-3. guava

- 스레드에 안전하지 않다.
- 내부적으로 System.nanoTime()을 사용한다.
  ```java
  Stopwatch stopwatch = Stopwatch.createStarted();
  // 실행 시간을 측정할 코드
  // ...
  stopwatch.stop(); // 선택
  System.out.println(stopwatch.elapsed(TimeUnit.MILLISECONDS));
  ```
  ```text
  12.3 ms
  ```


## 참고
https://stackoverflow.com/questions/3382954/measure-execution-time-for-a-java-method  
https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/util/StopWatch.html  
https://commons.apache.org/proper/commons-lang/apidocs/org/apache/commons/lang3/time/StopWatch.html  
https://guava.dev/releases/19.0/api/docs/com/google/common/base/Stopwatch.html  