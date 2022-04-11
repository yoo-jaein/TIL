# Asynchronous Spring MVC
Spring MVC로 비동기 프로그래밍하기

## BIO
> Blocking I/O, 가장 기본적인 커넥터 모델

BIO(Blocking I/O)는 가장 기본적인 방식의 I/O로 단방향 블로킹 방식으로 동작한다. 예를 들면, 새로운 소켓을 받기 위해 끊임없이 기다리고, 메시지를 전달할 때까지 연결된 소켓도 계속 기다리는 방식이다. 또한, 데이터를 보내는 쪽과 받는 쪽이 정해져 있어서 Stream (InputStream, OutputStream 등)이라는 용어를 사용하며, 읽고 쓰는 작업에서 블로킹되어 다른 일을 수행하지 못한다.  

BIO는 각 연결에 대해 스레드 풀에 하나의 스레드가 필요하다. 이러한 방식을 Request per Thread 모델이라 부른다. Request per Thread 모델을 음식점에 비유를 해보자. 음식점에 요리사가 한 명 있다. 그 요리사는 한 명의 손님에 대해서 주문을 받고 요리하고 완성하고 그 손님을 돌려보내기까지 다른 손님을 받지 못한다. 동시에 여러 손님을 받고 싶다면 요리사를 똑같이 늘려야 하는 구조다.  

BIO 커넥터로 들어오는 모든 연결은 스레드 풀에서 온 스레드가 하나씩 할당된다. 즉, 100명의 동시 사용자에게 서비스를 제공하려면 100개의 요청 스레드가 필요하다. 이렇게 한 번 할당된 스레드는 연결이 닫힐 때까지 해당 연결에 할당된 상태로 유지된다. 이것은 스레드가 요청을 처리하는 오랜 시간 동안(HTTP 연결을 유지하는 동안) Waiting 상태가 되는 것을 의미한다. 

### BIO의 한계
- 굉장히 직관적인 방식으로 동작하지만, 메시지가 전송되지 않는 경우 의도된 것인지 오류인지 알 수 없어서 제어가 어렵다. 그저 무한정 기다려야 한다. 
- 스레드 풀의 크기(스레드 개수)를 무작정 늘릴 수도 없다. 스레드도 각자 본인만의 리소스를 필요로 하기 때문에 많은 메모리가 소모될 것이다.
- 요청이 많은 서버에서 여러 스레드가 처음엔 Runnable 상태였다가 요청을 처리하는 동안 Waiting 상태가 되고 다시 Runnable 상태가 되면서 잦은 컨텍스트 스위칭에 따른 오버헤드가 발생할 수도 있다.
- 네트워크 프레임워크인 Tomcat의 BIO를 사용하는 경우 스레드 풀의 기본 크기는 200개인데, 이 200개의 스레드가 CPU Core를 점유하기 위해서 스레드들끼리 경합하는 현상도 발생한다. 대부분의 API 개발자들이 사용하는 머신 Core의 개수는 2~8개 정도이다. 200개의 스레드가 8개의 Core를 점유하기 위해 경합한다면 이 또한 큰 부하가 될 것이다. 

BIO의 많은 한계를 극복하기 위해 NIO가 등장했다.

## NIO
> New I/O, Nonblocking I/O

NIO(New I/O, Nonblocking I/O)는 논블로킹 방식으로 동작할 수 있는 I/O다. 양방향 입출력이 가능해서 Channel(SocketChannel 등)이라는 용어를 사용한다. 객체 하나로 읽기 쓰기 모두 가능하다.  

동기 논블로킹 방식. 애플리케이션에서 I/O를 요청한 후 즉시 리턴해서 다른 작업을 수행하다가, 특정 시간에 데이터가 준비되었는지 상태를 확인한다. 주기적으로 확인하다가 완료되었으면 종료하는 방식이다. 이 때 주기적으로 체크하는 방식을 폴링(Polling)이라 부른다. 그러나 이 방식은 작업이 완료되었는지 주기적으로 호출하기 때문에 여전히 불필요하게 자원을 사용한다.  

비동기 논블로킹 방식. I/O 요청을 한 후 동기 논블로킹 I/O와 마찬가지로 즉시 리턴된다. 그러나 데이터 준비가 완료되면 이벤트가 발생하여 알려주거나, 미리 등록해놓은 콜백을 통해 다음 작업이 진행된다. 그래서 동기 블로킹 방식이나 폴링 방식보다 자원을 더 효율적으로 사용할 수 있다. (어.. 그러면 톰캣 nio에서 쓰는 nio 방식은 동기 논블로킹인건가 비동기 논블로킹인건가? 폴러를 쓰니까 동기 논블로킹 같다. 그래서 비동기 논블로킹인 네티가 훨씬 성능이 조은 걸까?)

### Polling
NIO에는 폴링(Polling)이라는 새로운 개념이 등장한다. NIO에서 각각의 새로운 연결은 Poller로 전달된다. 처리할 연결에 대한 데이터가 있으면 Poller 스레드에 알린다. 그런 다음 Poller는 커넥터 스레드 풀에서 연결에 스레드를 할당하고 해당 스레드는 모든 데이터를 읽고 쓸 때까지 해당 연결에 할당된 상태를 유지한다. 이후 연결은 Poller로 다시 전달되어 Poller가 더 많은 데이터를 모니터링할 수 있다.

### Tomcat NIO with Spring MVC
Tomcat 7 버전까지 자바 BIO 커넥터를 사용했다. 그러나 NIO 커넥터가 등장하면서 BIO는 점차 사라졌고, Tomcat 8.5 버전에서 BIO 커넥터가 완전히 사라지면서 NIO, NIO2, ARP 기반의 커넥터만 사용할 수 있게 되었다. 최신 스프링 부트 서버에서는 대부분 Tomcat BIO 대신 NIO를 사용하고 있기 때문에 다음과 같은 nio 관련 로그를 찾을 수 있을 것이다.
```log
2022-04-12 00:22:44.409  INFO 13744 --- [nio-8080-exec-2] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring DispatcherServlet 'dispatcherServlet'
2022-04-12 00:22:44.410  INFO 13744 --- [nio-8080-exec-2] o.s.web.servlet.DispatcherServlet        : Initializing Servlet 'dispatcherServlet'
2022-04-12 00:22:44.418  INFO 13744 --- [nio-8080-exec-2] o.s.web.servlet.DispatcherServlet        : Completed initialization in 7 ms
...
2022-04-12 00:23:39.855 ERROR 13744 --- [nio-8080-exec-6] c.y.o.c.handler.RestExceptionHandler     : exception
```
위 로그는 Tomcat 9.x 버전을 사용하는 Spring Boot 2.6.0 MVC 서버의 로그다. nio-8080-exec-2는 실제 ServletThread로 .. 그런데 Spring MVC처럼 여전히 애플리케이션 코드가 블로킹되는 경우에도 과연 NIO 커넥터가 효과적일까? NIO 커넥터를 사용하면 요청을 처리하는 모든 과정이 논블로킹으로 동작할까? 데이터베이스 읽기/쓰기, 파일 시스템 읽기, 외부 API 호출에선 어떤 일이 일어날까?  

### 여전히 애플리케이션 코드가 블로킹되는 경우에도 과연 NIO 커넥터가 효과적일까?
먼저, 애플리케이션 코드에서 블로킹이 발생해도 NIO가 유익한가에 대한 답변은 그렇다. NIO 커넥터는 처음부터 애플리케이션이 어딘가에서 블로킹된다는 가정 하에 만들어진 것이다. NIO 커넥터에는 기본적으로 여러 개의 소켓 플레이스홀더가 있고, NIO 커넥터는 새로 들어오는 요청에 대해 응답한다.

Tomcat NIO에는 스레드 풀이 여전히 존재한다. Tomcat NIO 커넥터는 스레드 풀을 이용해서 논블로킹으로 동작하는 것이다. (Netty도 스레드 풀을 갖고 있다. 논블로킹이라고 해서 무조건 서버가 싱글 스레드로 동작하는 것은 아니다.)

스레드 풀이 존재하지만 실제 사용되는 스레드의 수는 애플리케이션이 처리할 수 있는 연결의 개수보다 훨씬 적다. Tomcat BIO일 때 보다도 Tomcat 커넥터 간에 성능 차이는 있지만 서블릿 자체가 블록될 때 .. Tomcat이 처리할 수 있는 동시 요청 수의 차이는 NIO를 사용할때가 훨씬 좋다.

### NIO 커넥터를 사용하면 요청을 처리하는 모든 과정이 논블로킹으로 동작할까?

### 데이터베이스 읽기/쓰기, 파일 시스템 읽기, 외부 API 호출에선 어떤 일이 일어날까?

커넥터 코드는 각 Selector를 폴링해서 수신할/발신할 바이트가 있는지 확인한다. 주어진 요청에 대한 Selector는 정보를 계속 수신한다.
여전히 애플리케이션 코드는 동기 방식으로 실행된다.

논블로킹 애플리케이션은 스레드가 블록되지 않는 방식으로 작성된다. 논블로킹 애플리케이션은 일반적으로 메시지 전달 또는 이벤트로 구현된다. 요청 이벤트를 보낸 다음 다른 스레드에서 다른 시간에 비동기적으로 응답을 받는다.

예를 들어, 데이터베이스에서 데이터를 쓰는 작업을 하는 논블로킹 애플리케이션의 경우 API 요청 메시지를 보내고, 데이터베이스 액세스 계층에 메시지를 보내고, 드라이버에게 메시지를 보내고, 데이터베이스에서 데이터 쓰기가 완료된 경우 알림을 받는다. 콜백에서 차례로 메시지를 전달한다. 이렇게 논블로킹 애플리케이션에는 모든 것이 메시지 전달과 콜백으로 구성된다.

한 가지 문제는, 코드의 어느 지점에서든지 스레드가 블로킹되면 모든 것이 hell로 간다는 것이다. 그래서 Webflux 서버와 같은 논블로킹 애플리케이션의 모든 코드와 사용하는 라이브러리가 블로킹되지 않아야 한다.

## Tomcat BIO vs NIO
|                       | Java Blocking Connector | Java Non-Blocking Connector |
|-----------------------|-------------------------|-----------------------------|
| Classname             | BIO                     | NIO                         |
| Support Polling       | NO                      | YES                         |
| Polling Size          | N/A                     | maxConnections              |
| Read Request Headers  | Blocking                | Non Blocking                |
| Read Request Body     | Blocking                | Blocking                    |
| Write Response        | Blocking                | Blocking                    |
| Wait for next Request | Blocking                | Non Blocking                |
| SSL Handshake         | Blocking                | Non Blocking                |
| Max Connections       | maxConnections          | maxConnections              |
- https://tomcat.apache.org/tomcat-7.0-doc/config/http.html#Connector_Comparison

## RestTemplate vs WebClient
Spring의 HTTP인 요청 라이브러리 RestTemplate은 블로킹 IO이고, Spring Webflux에 들어있는 WebClient는 논블로킹 IO다.
요청하고 응답을 받을 때까지 3초가 걸리는 API가 있다고 가정했을 때, RestTemplate을 이용해서 이 API를 10번 콜을 하는 경우 약 30초 이상의 시간이 소요될 것이다. 반면에 WebClient를 이용해서 이 API를 10번 호출하는 경우엔 1번 호출했을 때와 유사하게 3.x초, 4.x초 정도의 시간이 소요될 것이다. WebClient는 Blocking I/O인 RestTemplate과 비교해서 굉장히 효율적이기 때문에 이제는 Spring MVC에서도 RestTemplate보단 WebClient를 사용하는 것을 권장하고 있다.

## Spring MVC의 블로킹 IO
RestTemplate

## Spring MVC의 논블로킹 IO
> 주의! 테스트를 시도한 횟수가 굉장히 적고, 매우 극단적인 사례임. 그저 궁금해서 한 것.

Spring MVC에서 WebClient를 사용하여 외부 API를 호출했을 때 응답 시간 재보기

### Spring MVC 서버
```java
@RestController
@RequiredArgsConstructor
public class MvcController {

	private final DemoService demoService;

	@GetMapping("int")
	public List<Integer> getNumber() {
		return demoService.getNumber();
	}
}
```
```java
@Service
public class DemoService {
	
	private final WebClient webClient;

	public DemoService(WebClient.Builder webClientBuilder) {
		this.webClient = webClientBuilder.baseUrl("http://localhost:8080").build();
	}

	public List<Integer> getNumber() {
		List<Integer> result = new ArrayList<>();
		for (int i = 0; i < 10000; i++) {
			result.add(this.webClient
				.get()
				.uri("/int")
				.retrieve()
				.bodyToMono(Integer.class)
				.flux()
				.toStream()
				.findFirst()
				.orElse(0));
		}

		return result;
	}
}
```

### Spring Webflux 서버
```java
@RestController
public class WebfluxController {

	@GetMapping("/int")
	public Mono<Integer> demo() {
		return Mono.just(1);
	}
}
```

### Spring MVC WebClient <-> Spring Webflux 결과
Spring MVC 서버에서 Webflux 서버 API를 10,000번 호출했을 때 응답 속도 5.86s, 4.51s, 5.28s

### Spring MVC 서버
```java
@RestController
public class MVC2Controller {

	@GetMapping("/int")
	public Integer demo() {
		return Mono.just(1);
	}
}
```

### Spring MVC WebClient <-> Spring MVC 결과
Spring MVC 서버에서 Spring MVC 서버 API를 10,000번 호출했을 때 응답 속도 7.65s, 6.90s, 7.11s

## 참고
https://honeyinfo7.tistory.com/277  
https://ooeunz.tistory.com/149  
https://velog.io/@jihoson94/EventLoopModelInSpring  