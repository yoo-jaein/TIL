# Hot vs Cold Stream
핫 스트림과 콜드 스트림

## Publisher의 종류
Publisher는 콜드 스트림과 핫 스트림으로 분류될 수 있다. Reactor의 일반적인 Mono, Flux는 모두 비동기식 데이터 시퀀스를 나타내고 subscribe하기 전까진 아무 일도 일어나지 않는다. 이 방식을 콜드 스트림이라 부른다.

## 콜드 스트림
콜드 스트림(Cold Stream, Cold Sequences)은 지연 스트림(Lazy Stream)이라고도 한다. subscribe하기 전까지 동작하지 않으며, subscribe할 때마다 매번 새로운 데이터를 발행한다. Webflux에서 사용하는 Reactor는 일반적으로 콜드 스트림 방식을 사용한다. 누군가가 데이터에 관심을 가질 때까지 실행되지 않는 비동기 작업을 나타내는 데 사용된다.

## 핫 스트림
핫 스트림(Hot Stream, Hot Sequences)은 콜드 스트림과 다르게 subscribe하기 전에 미리 데이터를 생성할 수 있다. subscribe할 때마다 미리 만들어 놓은 데이터를 동작시킬 수 있고, 이때 새로운 데이터가 발행되지는 않는다. Mono.just는 핫 스트림이다. 구독하지 않아도 메서드가 실행된다. 반면 Mono.create는 콜드 스트림이다. subscribe하지 않으면 아무 일도 일어나지 않으며 해당 데이터가 손실된다.

## 콜드 스트림 -> 핫 스트림
```java
ConnectableFlux<Object> publish = Flux.create(fluxSink -> {
    while(true) {
        fluxSink.next(System.currentTimeMillis());
    }
})
  .sample(ofSeconds(2)) // 2초 간격을 둬서 발행되는 데이터 양을 줄일 수 있다.
  .publish();
```
publish()를 호출하면 ConnectableFlux가 생성된다. 

```java
publish.subscribe(System.out::println);
publish.subscribe(System.out::println);
publish.connect(); //connect를 호출해야지 데이터가 방출된다.
```

## 핫 스트림 -> 콜드 스트림
```java
Mono<Integer> justMono = Mono.just((new Random()).nextInt(10));
//this will print same random number thrice
for (int i = 0; i < 3; i++) {
	justMono.subscribe(x -> {System.out.println("Just Mono: " + x);});
}

Mono<Integer> deferMono = Mono.defer(() -> Mono.just((new Random()).nextInt(10)));
//this might print three different random numbers
for (int i = 0; i < 3; i++) {
	deferMono.subscribe(x -> {System.out.println("Defer Mono: " + x);});
}
```
defer를 사용하면 모든 구독에서 새로운 데이터를 발행한다.

## 참고
https://projectreactor.io/docs/core/snapshot/reference/#reactor.hotCold    
https://medium.com/reactive-programming-in-springboot-using-webflux/hot-vs-cold-reactive-streams-2314ec983f5b  
https://www.baeldung.com/reactor-core  
https://www.baeldung.com/java-mono-defer  
https://stackoverflow.com/questions/55955567/what-does-mono-defer-do  