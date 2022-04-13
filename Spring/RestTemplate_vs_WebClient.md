# RestTemplate vs WebClient
RestTemplate과 WebClient 비교하기

## RestTemplate
스프링 부트 애플리케이션이 원격 REST 서비스를 호출하는 경우 RestTemplate 또는 WebClient를 사용한다.

```java
import org.springframework.boot.web.client.RestTemplateBuilder;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;

@Service
public class MyService {

    private final RestTemplate restTemplate;

    public MyService(RestTemplateBuilder restTemplateBuilder) {
        this.restTemplate = restTemplateBuilder.build();
    }

    public Details someRestCall(String name) {
        return this.restTemplate.getForObject("/{name}/details", Details.class, name);
    }

}
```

- org.springframework.web.client.RestTemplate
- HTTP 요청을 수행하는 ```Synchronous```, ```Blocking``` 클라이언트
  - 호출을 하면 응답이 돌아올 때까지 기다려야 한다.

오랫동안 Spring은 RestTemplate을 웹 클라이언트 추상화로 제공했다. RestTemplate은 내부적으로 요청당 스레드 모델을 기반으로 하는 Java Servlet API를 사용한다. 따라서 웹 클라이언트는 응답을 받을 때까지 스레드가 블로킹된다. 이는 요청이 많을 때 문제가 될 수 있다. 스레드 풀이 모두 소진되거나 빈번한 CPU 컨텍스트 스위칭으로 성능이 저하될 가능성이 있다.

> NOTE: As of 5.0 this class is in maintenance mode, with only minor requests for changes and bugs to be accepted going forward. Please, consider using the org.springframework.web.reactive.client.WebClient which has a more modern API and supports sync, async, and streaming scenarios.
- **Spring 5.0부터 유지 관리 모드(maintenance mode)가 되었다.** 앞으로 RestTemplate에는 마이너한 변경만 허용된다.
- 따라서, RestTemplate 대신에 최신 API를 제공하고 Synchronous, Asynchronous 및 streaming 시나리오를 지원하는 **WebClient를 사용할 것을 추천한다.**

## WebClient
```java
import org.neo4j.cypherdsl.core.Relationship.Details;
import reactor.core.publisher.Mono;

import org.springframework.stereotype.Service;
import org.springframework.web.reactive.function.client.WebClient;

@Service
public class MyService {

    private final WebClient webClient;

    public MyService(WebClient.Builder webClientBuilder) {
        this.webClient = webClientBuilder.baseUrl("https://example.org").build();
    }

    public Mono<Details> someRestCall(String name) {
        return this.webClient.get().uri("/{name}/details", name).retrieve().bodyToMono(Details.class);
    }

}
```
- org.springframework.web.reactive.function.client.WebClient
- HTTP 요청을 수행하는 ```Asynchronous```, ```Non-blocking```, ```Reactive``` 클라이언트
  - 호출을 하면 응답이 돌아올 때까지 기다릴 필요가 없다. 응답이 있을 때 알림을 받는다.
- RestTemplate보다 적은 스레드와 리소스를 사용하면서 더 많은 요청을 처리할 수 있다. 
- WebClient는 이벤트 루프 스레드와 커넥션 풀을 포함하여 Reactor Netty의 자원을 사용한다. 만약 Spring Webflux를 사용하는 경우 WebClient는 Webflux의 이벤트 루프와 자원을 공유한다. ([참고](https://piotrminkowski.com/2020/03/30/a-deep-dive-into-spring-webflux-threading-model/))
- Sync, Async 모두를 지원하기 때문에 RestTemplate을 대체할 수 있다.

### WebClient 예제 코드
#### 생성
```java
static WebClient create();
static WebClient create(String baseUrl);
static WebClient.Builder builder();
```

WebClient는 static 메서드를 통해 생성할 수 있다. 이 중 builder()를 사용하면 헤더, 쿠키, 필터 등 다양한 기능을 추가하기 좋다.

```java
WebClient client = WebClient.builder()
        .codecs(configurer -> ... )
        .build();
```

#### 복제
WebClient는 한 번 만들어지면 변경할 수 없다. 대신 다음과 같이 복제하여 사용할 수 있다.

```java
WebClient client1 = WebClient.builder()
        .filter(filterA).filter(filterB).build();

WebClient client2 = client1.mutate()
        .filter(filterC).filter(filterD).build();
```

#### 타임아웃
```java
import io.netty.channel.ChannelOption;
import io.netty.handler.timeout.ReadTimeoutHandler;
import io.netty.handler.timeout.WriteTimeoutHandler;

HttpClient httpClient = HttpClient.create()
        .option(ChannelOption.CONNECT_TIMEOUT_MILLIS, 10000) // 커넥션 타임아웃 설정
        .doOnConnected(conn -> conn // 읽기 및 쓰기 타임아웃 설정
            .addHandlerLast(new ReadTimeoutHandler(10)) 
            .addHandlerLast(new WriteTimeoutHandler(10)));

WebClient webClient = WebClient.builder()
        .clientConnector(new ReactorClientHttpConnector(httpClient))
        .build();
```

```java
HttpClient httpClient = HttpClient.create()
        .responseTimeout(Duration.ofSeconds(2)); // 응답 타임아웃 설정

WebClient webClient = WebClient.builder()
        .clientConnector(new ReactorClientHttpConnector(httpClient))
        .build();
```

```java
WebClient.create().get()
        .uri("https://example.org/path") // 특정 요청에 대한 응답 타임아웃 설정
        .httpRequest(httpRequest -> {
            HttpClientRequest reactorRequest = httpRequest.getNativeRequest();
            reactorRequest.responseTimeout(Duration.ofSeconds(2));
        })
        .retrieve()
        .bodyToMono(String.class);


WebClient webClient = WebClient.builder()
        .clientConnector(new ReactorClientHttpConnector(httpClient))
        .build();
```

#### 요청
```java
Mono<Person> personMono = ... ;

Mono<Void> result = client.post()
        .uri("/persons/{id}", id)
        .contentType(MediaType.APPLICATION_JSON)
        .body(personMono, Person.class)
        .retrieve()
        .bodyToMono(Void.class);
```

#### URI path
```java
// /products/2/attributes/13
client.get()
        .uri(uriBuilder - > uriBuilder
        .path("/products/{id}/attributes/{attributeId}")
        .build(2, 13))
        .retrieve();
```

#### Request body
```java
client.post()
        .uri("/user/" + userId)
        .bodyValue(request) // UserRequest request
// .retrieve();
```

#### Query parameter
```java
// /products/?name=AndroidPhone&color=black&deliveryDate=13/04/2019"
client.get()
        .uri(uriBuilder - > uriBuilder
        .path("/products/")
        .queryParam("name", "AndroidPhone")
        .queryParam("color", "black")
        .queryParam("deliveryDate", "13/04/2019")
        .build())
        .retrieve();
```

#### Multi part
```java
MultipartBodyBuilder builder = new MultipartBodyBuilder();
builder.part("fieldPart", "fieldValue");
builder.part("filePart1", new FileSystemResource("...logo.png"));
builder.part("jsonPart", new Person("Jason"));
builder.part("myPart", part); // Part from a server request

MultiValueMap<String, HttpEntity<?>> parts = builder.build();

Mono<Void> result = client.post()
        .uri("/path", id)
        .body(builder.build())
        .retrieve()
        .bodyToMono(Void.class);
```

#### 응답 읽기
```java
WebClient client = WebClient.create("https://example.org");

Mono<Person> result = client.get()
        .uri("/persons/{id}", id).accept(MediaType.APPLICATION_JSON)
        .retrieve()
        .bodyToMono(Person.class);
```

#### 응답 에러 처리하기
```java
Mono<Person> result = client.get()
        .uri("/persons/{id}", id).accept(MediaType.APPLICATION_JSON)
        .retrieve()
        .onStatus(HttpStatus::is4xxClientError, response -> ...)
        .onStatus(HttpStatus::is5xxServerError, response -> ...)
        .bodyToMono(Person.class);
```

#### Webclient 동기 호출
```java
Person person = client.get().uri("/person/{id}", i).retrieve()
    .bodyToMono(Person.class)
    .block();

List<Person> persons = client.get().uri("/persons").retrieve()
    .bodyToFlux(Person.class)
    .collectList()
    .block();
```

## 참고
https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#io.rest-client  
https://docs.spring.io/spring-framework/docs/5.3.18/reference/html/web-reactive.html#webflux-client  
https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html
https://www.baeldung.com/spring-webclient-resttemplate  