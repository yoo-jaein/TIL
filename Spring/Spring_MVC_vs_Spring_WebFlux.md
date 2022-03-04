# Spring MVC vs Spring WebFlux

## Spring MVC
Spring MVC는 스레드 per Request 모델로 구현되어 있다. 서블릿 3.1 이상 스펙으로 구현된 서블릿 컨테이너, WAS.
브라우저가 서버로 Request를 날리면 서버는 이 Request를 처리하기 위해서 스레드풀에 있는 스레드 하나를 뽑아서 할당한다. 할당된 스레드는 요청을 받고 응답할 때까지 모든 처리를 담당하게 된다. 스레드 하나가 모든 일을 처리한다. 클라이언트에게 응답을 마치면 할당된 스레드 다시 스레드풀로 돌아간다. 참고로 스프링 부트에서 제공하는 자동 설정으로 Tomcat을 설정하는 경우 스레드풀의 스레드 개수가 200개이다.

분산 시스템으로 설계된 서비스는 API 서버가 아닌 다른 API 서버의 Rest API를 호출하여 데이터를 통합하는 경우가 매우 흔하다. A 서버의 스레드 1번이 B 서버의 Rest API를 호출하는 경우, B 서버가 데이터를 처리하는 시간 동안 A 서버의 스레드 1번은 블로킹된다. 즉, 기다리는 상태가 된다. 만약 B 서버가 데이터를 처리하는 시간이 길어지면 A 서버의 블로킹 시간 또한 길어지게 된다.

블로킹 시간이 길어지면 A 서버의 스레드 1번의 상태는 어떻게 될까? 네트워크 I/O가 끝나길 기다리는 동안 스레드 상태는 Waiting이 된다. 네트워크 I/O가 끝나면 해당 스레드는 다시 다음 코드를 처리하기 위해서 Runnable 상태로 다시 변경된다. 시스템에 부하가 낮은 상태에서는 이런 일련의 과정이 큰 문제가 되지 않는다. Runnable - Waiting - Runnable. 하지만 시스템에 부하가 높은 상태에서 스레드 상태가 Runnable에서 Waiting으로 바뀌는 것, 즉 컨텍스트 스위칭이 되고 스레드의 데이터가 계속해서 로딩하는 오버헤드가 문제가 된다.

또한, Tomcat의 기본 스레드 개수는 200개이다. 이 200개의 스레드가 CPU Core를 점유하기 위해서 스레드들이 경합하는 현상도 발생한다. 대부분의 API 개발자들이 사용하는 머신 Core의 개수는 2~8개 정도이다. 200개의 스레드가 8개의 Core를 점유하기 위해 경합한다면 이 또한 큰 부하가 될 것이다.

## Spring WebFlux
Spring WebFlux는 Event Loop 모델로 동작한다. 사용자들의 요청이나 애플리케이션 내부에서 처리해야 되는 작업들은 모두 Event라는 단위로 관리되고, Event queue에 적재되어 순서대로 처리되는 구조이다. 그리고 Event를 처리하는 스레드풀이 존재한다. 이 스레드풀은 순차적으로 Event를 처리한다고 해서 Event Loop라고 부르기도 한다.

Event Loop는 Event queue에서 Event를 뽑아서 하나씩 처리한다. Spring WebFlux는 리액터 라이브러리와 netty를 기반으로 동작한다. netty는 Tomcat과 달리 스레드풀의 스레드 개수는 머신의 Core 개수의 2배이다. 

Spring MVC처럼 스레드가 블록되어 I/O가 끝날 때까지 Waiting하는 대신 I/O가 시작되기 전 작업도 Event I/O가 종료되면 처리할 작업도 Event로 만들어져 Queue에 들어간다. NIO를 이용하여 I/O작업 처리를 하기 때문에 스레드 상태가 블록되지 않는다.

스레드 per Request 방식보다 컨텍스트 스위칭 오버헤드가 줄어들고 스레드 숫자도 작기 때문에 Core를 차지하기 위한 경합도 줄어든다. 

높은 처리량이 가능한 이유는 Event Loop와 Non-blocking I/O를 사용하기 때문이다. Event Loop의 스레드를 일하는데만 집중하여 성능을 쥐어짜기 때문에 처리량이 높게 나온다. 즉, 다른 일을 할 수 있는 프리한 스레드가 많기 때문이다.

만약 Non-blocking I/O 대신 blocking I/O를 사용하면 어떻게 될까? WebFlux는 상태적으로 적은 스레드풀을 유지하기 때문에 CPU 사용량이 높은 작업이 많거나 혹은 블로킹 I/O를 이용하여 프로그래밍하면 Event Loop가 Event를 빠르게 처리할 수 없다. Runnable 상태의 스레드가 CPU를 점유하여 전반적인 성능 하락이 발생한다. 동영상 인코딩, 혹은 암호화 모듈을 이용하여 암호화 복호화하는 애플리케이션은 WebFlux가 적절하지 않을 수 있다.

## Reactor Meltdown
Webflux가 무조건 빠르고 좋은 것은 아니다. Webflux 애플리케이션의 비지니스 로직들이 모두 Async + NonBlocking 으로 되어있다면 빠를 수 있다. Webflux의 어떤 Event 하나에 blocking 로직이 들어가게 될 경우, 해당 Event를 처리하는 스레드만 성능이 저하되는게 아니라 Event Loop 전체의 성능에 문제가 발생한다. Event Loop는 여러 채널의 Event를 모니터링하고 발생된 이벤트를 처리하고 큐에 있는 모든 Runnable을 가져가서 처리한다. 싱글 스레드로 동작하기 때문에 한쪽에서 지연이 발생하면 전체 루프에 영향이 간다. 한 쪽 채널에서 지연이 발생하면 다른 모든 채널에 지연이 발생해서 결국 모든 요청이 지연되는데 이걸 Reactor Meltdown이라고 한다. 그래서 Webflux에서 블로킹되는 동작을 매우 조심해야 하며, blocking되는 동작은 별도의 스케줄러를 통해 동작할 수 있도록 격리해줘야 한다.

## 참고
[[NHN FORWARD 2020] 내가 만든 WebFlux가 느렸던 이유](https://youtu.be/I0zMm6wIbRI)  
[[if(kakao)2021]Webflux로 막힘없는 프로젝트 만들기](https://if.kakao.com/session/107)  
https://docs.spring.io/spring-framework/docs/current/reference/html/web-reactive.html#webflux-framework-choice  