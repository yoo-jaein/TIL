# Unix epoch time
Unix time, Epoch(에포크) time, Unix epoch time, Posix(파직스) time, Unix timestamp  

## Unix time
```text
1635863260 == Tuesday, November 2, 2021 11:27:40 PM GMT+09:00
```

- Unix time은 1970년 1월 1일 00:00:00 (UTC, 표준시) 이후로 현재까지 몇 초가 경과되었는지 기록하는 값이다.
- 'epoch'라는 용어 자체는 1970년 1월 1일 00:00:00 (UTC) 시점을 가리키지만 종종 Unix time의 동의어로 사용된다.
- 10자리의 초 단위, 13자리의 밀리초 단위, 16자리의 마이크로초 단위가 있다.
- 윤초를 제외하기 때문에 Unix 시간은 매일이 정확히 86400초를 포함하는 것처럼 처리된다.
- Unix 계열의 운영 체제 및 파일 형식에서 널리 사용된다.


## 자바에서 Unix time 구하기
### 방법 1: System.currentTimeMillis()
```java
long now = System.currentTimeMillis();
```
- 자바에서 현재 시간은 System.currentTimeMillis()를 통해 구할 수 있다.
- 이 메서드는 long 타입으로 밀리초 단위의 Unix time을 반환한다.

### 방법 2: Instant.now().toEpochMilli()
```java
long now = Instant.now().toEpochMilli();
```
- 또 다른 방법으로 자바 8의 java.time.Instant를 통해 현재 시간을 구할 수 있다.
- getEpochSecond()를 통해 초 단위, toEpochMilli()를 통해 밀리초 단위를 구할 수 있다.
- Instant는 Unix time뿐만 아니라 시간 계산, 비교 등 다양한 메서드를 제공한다.

## Unix time 변환하기
https://www.epochconverter.com/

## 참고
https://en.wikipedia.org/wiki/Unix_time  
https://docs.oracle.com/javase/8/docs/api/java/time/Instant.html  