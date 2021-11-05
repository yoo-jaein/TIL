# Instant
java.time.Instant

## Epoch time 계산하기
```java
Instant.now().plus(5, ChronoUnit.DAYS).getEpochSecond() 
```
- 지금으로부터 5일 후의 초 단위 Epoch time을 구한다.

```java
Instant.now().minus(5, ChronoUnit.DAYS).toEpochMilli()
```
- 지금으로부터 5일 전의 밀리초 단위 Epoch time을 구한다.

## 주의! UnsupportedTemporalTypeException
```java
Instant.now().plus(1, ChronoUnit.YEARS).getEpochSecond() 
```
- plus()에서 단위를 ```YEARS```로 설정한 위의 코드는 ```UnsupportedTemporalTypeException```를 일으킨다.

```java
// Instant#plus
@Override
public Instant plus(long amountToAdd, TemporalUnit unit) {
    if (unit instanceof ChronoUnit) {
        switch ((ChronoUnit) unit) {
            case NANOS: return plusNanos(amountToAdd);
            case MICROS: return plus(amountToAdd / 1000_000, (amountToAdd % 1000_000) * 1000);
            case MILLIS: return plusMillis(amountToAdd);
            case SECONDS: return plusSeconds(amountToAdd);
            case MINUTES: return plusSeconds(Math.multiplyExact(amountToAdd, SECONDS_PER_MINUTE));
            case HOURS: return plusSeconds(Math.multiplyExact(amountToAdd, SECONDS_PER_HOUR));
            case HALF_DAYS: return plusSeconds(Math.multiplyExact(amountToAdd, SECONDS_PER_DAY / 2));
            case DAYS: return plusSeconds(Math.multiplyExact(amountToAdd, SECONDS_PER_DAY));
        }
        throw new UnsupportedTemporalTypeException("Unsupported unit: " + unit);
    }
    return unit.addTo(this, amountToAdd);
}
```

- 위는 ```Instant```의 plus() 구현 코드다. unit의 타입으로 ```ChronoUnit```을 사용하는데, ```NANOS```, ```MICROS```, ```MILLIS```, ```SECONDS```, ```MINUTES```, ```HOURS```, ```HALF_DAYS```, ```DAYS```를 제외한 나머지 ```ChronoUnit``` 타입은 사용할 수 없다.

## 참고
https://docs.oracle.com/javase/8/docs/api/java/time/Instant.html  
https://stackoverflow.com/questions/39907925/why-instant-does-not-support-operations-with-chronounit-years  
