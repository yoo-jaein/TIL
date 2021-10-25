# RestTemplate vs WebClient
RestTemplate과 WebClient 비교하기

## RestTemplate
- **org.springframework.web.client.RestTemplate**
- HTTP 요청을 수행하는 ```Synchronous```, ```Blocking``` 클라이언트
  - 호출을 하면 응답이 돌아올 때까지 기다려야 한다.

- 오랫동안 Spring은 RestTemplate을 웹 클라이언트 추상화로 제공했다. RestTemplate은 내부적으로 요청당 스레드 모델을 기반으로 하는 Java Servlet API를 사용한다. 따라서 웹 클라이언트는 응답을 받을 때까지 스레드가 차단된다(Blocking). 
- 차단은 요청이 많을 때 문제가 될 수 있다. 각 스레드가 일정량의 메모리와 CPU 자원을 소비한다. 스레드 풀이 모두 소진되거나 빈번한 CPU 컨텍스트 전환으로 성능이 저하될 가능성이 있다.

> NOTE: As of 5.0 this class is in maintenance mode, with only minor requests for changes and bugs to be accepted going forward. Please, consider using the org.springframework.web.reactive.client.WebClient which has a more modern API and supports sync, async, and streaming scenarios.
- **Spring 5.0부터 유지 관리 모드(maintenance mode)가 되었다.** 앞으로 RestTemplate에는 마이너한 변경만 허용된다.
- 따라서, RestTemplate 대신에 최신 API를 제공하고 Synchronous, Asynchronous 및 streaming 시나리오를 지원하는 **WebClient를 사용할 것을 추천한다.**

## WebClient
- **org.springframework.web.reactive.function.client.WebClient**
- HTTP 요청을 수행하는 ```Asynchronous```, ```Non-blocking```, ```Reactive``` 클라이언트
  - 호출을 하면 응답이 돌아올 때까지 기다릴 필요가 없다. 응답이 있을 때 알림을 받는다.
- **Spring Reactive** 프레임워크에서 제공하는 솔루션을 사용한다. **이벤트 기반 아키텍처**를 사용하기 때문에 RestTemplate의 Sync & Blocking 방식에 비해 더 적은 스레드와 리소스를 사용하면서 더 많은 요청을 처리할 수 있다. 
- Sync, Async 모두를 지원하기 때문에 RestTemplate을 대체할 수 있다.

## 결론
**Spring MVC에서도 RestTemplate을 WebClient로 변경하는게 좋다.**

## 참고
https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html  
https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/reactive/function/client/WebClient.html  
https://www.baeldung.com/spring-webclient-resttemplate  