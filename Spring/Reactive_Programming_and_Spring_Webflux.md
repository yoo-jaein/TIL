# Reactive Programming and Spring Webflux
반응형 프로그래밍과 스프링 부트

## 관련 용어 정리
- Reactive는 I/O 이벤트에 반응하는 네트워크 컴포넌트, 마우스 이벤트에 반응하는 UI 컨트롤러 등 어떤 변화에 반응하는 것을 뜻한다.
- Reactive manifesto는 리액티브 애플리케이션과 리액티브 시스템이 지켜야 할 원칙을 정의한 것이다.
- Reactive Streams (Project)는 비동기 데이터를 순서대로 다루고 논블로킹 백 프레셔를 전제해서 처리하는 기술 명세다. 총 4개의 인터페이스 Publisher, Subscriber, Subscription, Processor로 구성되어 있다. Reactive Streams를 구현한 라이브러리에는 자바 9 Flow, Akka, Project Reactor, RxJava 등이 있다. (Spring Webflux는 내부적으로 Reactor를 사용한다.)
- Reactive programming은 Reactive Streams을 사용하는 프로그래밍이다.

[Blocking-NonBlocking-Synchronous-Asynchronous 참고](https://homoefficio.github.io/2017/02/19/Blocking-NonBlocking-Synchronous-Asynchronous/)
- 블로킹(Blocking)과 논블로킹(Non-blocking)은 호출되는 함수(callee)가 바로 리턴되는지에 따라 구분된다. 호출된 함수(callee)가 자신의 작업을 모두 마칠때까지 호출한 함수(caller)에게 제어권을 넘겨주지 않고 대기하게 만든다면 블로킹된 것이다. 만약 호출된 함수(callee)가 즉시 리턴해서 호출한 함수(caller)에게 제어권을 넘겨주고 호출한 함수(caller)가 다른 일을 할 수 있는 기회를 주면 논블로킹이다. 
- 동기(Synchronous)와 비동기(Asynchronous)는 호출되는 함수(callee)의 작업 완료 여부를 누가 확인하냐에 따라 구분된다. 호출한 함수(caller)가 호출된 함수(callee)의 작업이 완료된 후 리턴을 기다리거나, 호출된 함수(callee)로부터 바로 리턴받더라도 (블록/논블록 상관 없이) 호출한 함수(caller)가 호출된 함수(callee)의 작업 완료 여부를 확인한다면 동기다. 호출된 함수(callee)에게 콜백을 전달해서 자신의 작업이 완료되었을 때 콜백을 실행하게 만들면 비동기다.

## Asynchronous & Non-blocking
유저의 요청 1건을 처리하기 위해 외부 시스템에 대한 수십여개의 요청이 필요한 시스템에서 가장 빠르고 효율적인 처리를 하려면 비동기 & 논블로킹으로 동작해야 한다. 만약 외부 시스템에 대한 수십 개의 요청이 모두 동기로 이루어진다면 수행시간은 전체 요청 수행시간의 합(sum(...request))이고, 비동기로 이루어진다면 수행시간은 전체 요청 수행시간 중 최대(max(...request))가 된다. 

또한, 아무리 비동기로 동작한다고 해도 블로킹 IO 기반의 동작을 하게 된다면 스레드 지옥에서 벗어날 수 없다. Spring Webflux는 이벤트 기반, 비동기 논블로킹 I/O를 통해 적은 수의 스레드로도 리소스를 효율적으로 사용할 수 있게 설계되었다. 이를 통해 성능상의 큰 이점을 얻을 수 있다.

## Reactive manifesto
![](https://www.reactivemanifesto.org/images/reactive-traits.svg)  

[리액티브 매니패스토](https://www.reactivemanifesto.org/)는 리액티브 애플리케이션과 리액티브 시스템 개발의 핵심 원칙을 공식적으로 정의한다. 

- 반응성(responsive) : 리액티브 시스템은 일정하고 예상 가능한 반응 시간을 제공한다. 반응성은 문제를 신속하게 감지하고 효과적으로 처리할 수 있음을 의미한다. 반응형 시스템은 빠르고 일관된 응답 시간을 제공하고 안정적인 상한선을 설정하여 일관된 서비스 품질을 제공하는 데 중점을 둔다. 일관적인 서비스 품질을 제공하면 오류 처리를 단순화할 수 있으며 애플리케이션에 대한 신뢰성을 높일 수 있다.
- 회복성(resilient) : 리액티브 시스템은 장애가 발생하더라도 반응성을 유지한다. 회복성은 컴포넌트 실행 복제, 컴포넌트의 시간과 공간 분리, 위임 등을 통해 달성된다. (각 구성 요소의 복구는 다른(외부) 구성 요소에 위임되며 필요한 경우 복제를 통해 고가용성을 보장한다.)
- 탄력성(elastic) : 리액티브 시스템은 다양한 워크로드에서 반응성을 유지한다. 리액티브 시스템은 다양한 입력을 처리하기 위해 입력 속도가 바뀐다면 입력 관련 서비스에 할당된 리소스를 늘리거나 줄임으로써 변화에 대응한다. 
- 메시지 기반(message-driven) : 회복성과 탄력성을 지원하려면 약한 결합, 고립, 위치 투명성 등을 지원할 수 있도록 시스템을 구성하는 컴포넌트의 경계를 명확하게 정의해야 한다. 리액티브 시스템을 구성하는 컴포넌트들 간의 통신에서 비동기 메시지 전달 방법을 사용한다. 메시지 전달 기법을 사용하면 시스템의 메시지 대기열을 형성 및 모니터링하고 필요할 때 백 프레셔를 적용하여 부하 관리, 탄력성 및 흐름 제어가 가능하다.

## Reactive programming
> [Reactive programming](https://howtodoinjava.com/spring-webflux/spring-webflux-tutorial/#:~:text=Reactive%20programming%20is%20a%20programming,the%20changes%20in%20those%20streams.) is a programming paradigm that promotes an asynchronous, non-blocking, event-driven approach to data processing.  

> In computing, [reactive programming](https://en.wikipedia.org/wiki/Reactive_programming) is a declarative programming paradigm concerned with data streams and the propagation of change.  

반응형 프로그래밍은 비동기식 논블로킹 데이터 스트림으로 프로그래밍하는 것을 말한다. 반응형 프로그래밍은 전통적인 CRUD 앱 개발보단 스트리밍 데이터가 있는 애플리케이션과 데이터를 소비하고 사용자에게 스트리밍하는 클라이언트에 적합하다. 스트림 자료 구조, 변수, 사용자의 input, 캐시 등 무엇이든 반응형 프로그래밍의 데이터 스트림이 될 수 있다.  

반응형 프로그래밍의 주요 이점은 다음과 같다.
- 복잡한 비동기/스레드 작업을 단순화한다.
- 높은 부하에 탄력적인 시스템을 구축할 수 있다.
- 백 프레셔를 쉽게 구현할 수 있다.
- 연산자를 통해 논리를 선언한다. 런타임에 개별 단계에서 데이터가 순차적으로 처리되는 반응 파이프라인이 형성된다.

## Back Pressure
[Reactive Streams 프로젝트](https://www.reactive-streams.org/)는 무한의 비동기 데이터를 순서대로 다루고 논블로킹 '백 프레셔'를 전제해서 처리하는 기술이다. 백 프레셔(역압력, Back pressure)는 발행-구독(pub-sub) 프로토콜에서 이벤트 스트림의 구독자가 발행자가 이벤트를 제공하는 속도보다 느린 속도로 이벤트를 소비하면서 문제가 발생하지 않도록 보장하는 장치다.  

동기적이고 순차적인 프로그래밍에서는 암묵적으로 백 프레셔가 제공된다. 블로킹 호출을 하는 경우 호출한 쪽에서 호출된 쪽의 작업이 끝날때 까지 기다리기 때문이다. 반면, 비동기 프로그래밍에서 논블로킹 호출을 하는 경우 이벤트를 공급하는 발행자가 지나치게 빨라서 그걸 소비하는 구독자 측이 죽지 않도록 흐름을 제어할 수 있는 기능이 꼭 필요하다.  

백 프레셔는 시스템이 강한 부하에서 붕괴되지 않고 탄력성을 갖도록 하는 중요한 피드백 매커니즘이다. 구독자(다운스트림 구성 요소)는 발행자(업스트림 구성 요소)에 스트레스를 받고 있다는 사실을 전달하여 부하를 줄여야 한다. 만약 시스템의 특성 상 발행자의 이벤트 발행 속도를 늦출 수 없다면, 버퍼에 담을지 버릴지 실패 처리할지 정해야 한다.  

## Spring Webflux
Spring WebFlux는 Spring 프레임워크의 일부이며 웹 애플리케이션에 대한 반응형 프로그래밍 지원을 제공한다. Spring 5.0에 추가된 Spring Webflux는 Reactive Streams의 구현인 [Project Reactor](https://projectreactor.io/) API를 기반으로 한다.  

Spring Webflux는 논블로킹 I/O와 함께 reactive 백 프레셔와 Servlet 3.1+를 지원한다. 따라서 Netty, Undertow, Jetty, Tomcat 또는 모든 Servlet 3.1+ 호환 서버에서 실행할 수 있다. 기본적으로 Reactor Netty를 내장 서버로 사용하여 애플리케이션을 실행한다. Reactor Netty는 비동기식 이벤트 기반 네트워크 애플리케이션 프레임워크다. 논블로킹, 백프레셔를 지원하는 TCP, HTTP, UDP 클라이언트와 서버를 제공하며, 이름에서 알 수 있듯이 Netty 프레임워크를 기반으로 한다.

## Reactive programming vs Imperative programming
- Reactive programming : 반응형 프로그래밍에서 발행자는 구독자에게 데이터를 보낸다. 데이터가 Push된다.
- Imperative programming : 명령형 프로그래밍에서는 컬렉션에 대한 반복을 수행하거나 데이터베이스에서 데이터 요청하는 등 명시적으로 데이터를 요청한다. 데이터가 Pull된다.

## Spring MVC Async vs Spring Webflux
Spring MVC, Spring Webflux 모두 동시성을 지원하지만 다음과 같은 중요한 차이점이 있다.
- Spring MVC Async는 Servlet 3.0 사양을 지원하지만 Spring Webflux는 Servlet 3.1 이상을 지원한다.
- 일반적인 서블릿 애플리케이션, Spring Async의 I/O 모델은 클라이언트와 통신하는 동안 블록된다. 따라서 느린 클라이언트에서 오랫 동안 블로킹되면 성능 문제가 발생할 수 있다. 반면 Spring Webflux는 non-blocking I/O 모델을 제공한다. Spring Webflux와 같은 논블로킹 서버는 작은 고정 크기의 스레드 풀(이벤트 루프 워커)을 사용하여 요청을 처리한다. 현재 스레드를 블록하지 않고 대신 콜백에 의존한다는 것은 흡수할 블로킹 콜이 없기 때문에 추가 스레드가 필요하지 않다는 것을 의미한다. 리액티브 WebClient는 이벤트 루프 방식으로 동작한다.
- Request body나 Request parts를 읽는 작업은 Spring Async에서 블록되지만 Spring Webflux에서는 블록되지 않는다.
- Spring Async에서 Filters와 Servlet은 동기적으로 작동하지만 Spring Webflux는 완전한 비동기 통신을 지원한다.
- Spring Webflux는 Netty 및 Undertow와 같이 Spring Async보다 광범위한 웹/애플리케이션 서버와 호환된다.
- Spring Webflux는 reactive 백 프레셔를 지원한다.

## Publisher
발행자: 구독자로부터 받은 수요에 따라 구독자에게 일련의 이벤트를 내보낸다. 발행자는 여러 구독자에게 서비스를 제공할 수 있다.

Mono와 Flux는 Reactor Stream의 Publisher 인터페이스를 구현하는 발행자 객체이며, 이 둘의 차이점은 발행하는 데이터 갯수다. Mono는 0개 혹은 1개의 데이터를 발행하며 Flux는 0개부터 N개까지의 데이터를 발행한다. 그래서 보통 여러 스트림을 하나의 결과로 모아줄 때 Mono를, 각각의 Mono를 합쳐서 하나의 컬렉션을 만들 때 Flux를 사용한다.

### Mono
```java
Mono<String> mono = Mono.just("Knowledgefactory.net");
Mono<String> mono = Mono.empty();
```
하나의 데이터를 전달할 때 onNext 신호가 발생한다. 최대 하나의 데이터를 내보낸 다음 onComplete 신호가 발생하며(대부분의 Mono 구현은 onNext를 호출한 후 즉시 onComplete를 호출해야 한다), 데이터를 전달하는 과정에서 오류가 발생하면 onError 신호가 발생한다.

### Flux
```java
Flux<String> flux = Flux.just("Java", "Kotlin", "C");
Flux<String> flux = Flux.fromArray(new String[]{"Java", "Kotlin", "C"});
Flux<String> flux = Flux.fromIterable(Arrays.asList("Java", "Kotlin", "C"));
 
//To subscribe call method
flux.subscribe();
```
하나의 데이터를 전달할 때마다 onNext 신호가 발생한다. Flux 내의 모든 데이터의 전달 처리가 완료 되면 onComplete 신호가 발생하며, 데이터를 전달하는 과정에서 오류가 발생하면 onError 신호가 발생한다.

## Subscriber
```java
public interface Subscriber<T> 
{
    public void onSubscribe(Subscription s);
    public void onNext(T t);
    public void onError(Throwable t);
    public void onComplete();
}
```

## Subscription
```java
public interface Subscription<T> 
{
    public void request(long n);
    public void cancel();
}
```

## Processor
```java
public interface Processor<T, R> extends Subscriber<T>, Publisher<R> { }
```

## WebClient의 bodyToMono()와 bodyToFlux()
WebClient는 불변이기 때문에 스레드 세이프하다.
- bodyToMono() : 단일 항목을 검색하는 데 사용된다. 0개 혹은 1개의 항목을 방출한다.
- bodyToFlux() : 여러 항목을 검색하는 데 사용된다. 0개부터 N개까지의 항목을 방출한다.

### bodyToMono()
bodyToMono() 메서드는 body를 Mono 인스턴스로 추출한다.

```java
Mono<Document> documentMono = webClient.get()
	.uri("/documents/{id}", "1")
	.retrieve()
	.bodyToMono(Employee.class);

documentMono.subscribe(System.out::println);
```

### bodyToFlux()
bodyToMono와 유사하지만 Mono 대신 Flux 인스턴스를 추출한다.

```java
Flux<Document> documentFlux = webClient.get()
	.uri("/documents")
	.retrieve()
	.bodyToFlux(Employee.class);

documentFlux.subscribe(System.out::println);
```

## Spring Webflux에서 예외를 처리하는 방법
- onErrorReturn() : 오류가 발생할 때마다 onErrorReturn()을 사용하여 정적 기본값을 반환한다. 
- onErrorResume() : 오류가 발생하면 fallback 메소드를 호출한다.
- onErrorMap() : 사용자 정의 예외를 제공한다.

## 참고
모던 자바 인 액션 17장 리액티브 프로그래밍  
https://docs.spring.io/spring-framework/docs/current/reference/html/web-reactive.html#webflux-why-reactive  
https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Mono.html  
https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Flux.html  
https://www.klusster.com/portfolios/adam-mathewz/contents/21920  
https://www.knowledgefactory.net/2021/02/spring-webflux-interview-questions.html  
https://homoefficio.github.io/2021/04/14/Reactive-Streams-with-Sequence-Diagram/  
https://docs.spring.io/spring-framework/docs/5.0.0.M1/spring-framework-reference/html/web-reactive.html  
https://techblog.woowahan.com/2667/  