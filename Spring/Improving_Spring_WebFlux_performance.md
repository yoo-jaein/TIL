# Improving Spring WebFlux performance
스프링 웹플럭스 애플리케이션의 성능 개선하기

## 성능 개선 사항
1. log() 지우기 : 로그를 남기는 역할. 실제로 blocking I/O를 일으키기 때문에 성능 저하가 쉽게 발생한다.
2. map() 대신 flatMap() 활용하기 : map()은 동기식 함수를 적용하여 Mono의 아이템을 변경하는데 사용하는 메서드. flatMap()은 async하게 Mono의 아이템을 변경하는데 사용하는 메서드. 즉, 비동기 처리 부분은 flatMap()으로 동기 처리 부분은 map()을 사용해야 한다. 그리고 너무 많은 map() 조합은 연산마다 객체를 생성하므로 GC의 대상이 많아지는 단점도 존재한다.
3. BlockHound 라이브러리 활용하기: blocking 코드를 찾아주는 라이브러리. reactor-core 3.3.0부터 내장. blocking하는 대표적인 메서드는 block(), blockFirst(), blockLast(). 테스트 케이스 대상 코드에 blocking 코드가 있는지 확인하는데 유용하다.
4. Lettuce 설정 확인하기 : Lettuce 커넥션에는 커맨드를 날릴 때마다 validation하는 설정이 있다. 내부에는 Redis ping 커맨드를 전송하고, 응답으로 OK를 받으면 개발자가 보낸 커맨드로 실행한다. 이때 ping 커맨드가 동기식으로 실행된다. Lettuce는 netty 기반 NIO의 비동기로 동작한다. 만약 커넥션 validation으로 설정했다면 성능 저하가 발생한다. - LettuceConnectionFactory#validateConnection()의 sync().ping()
5. Reactor Meltdown 피하기 : Reactor로 애플리케이션을 개발할 때 가능하면 모든 코드들은 비동기 Non-blocking으로 개발해야 한다. Reactive 라이브러리를 제공하는 경우 비동기 Non-blocking으로 개발할 수 있다. 하지만 상황에 따라서 동기식 blocking 코드로 개발해야 되는 경우도 있다. 이 때 Event Loop에 스레드들이 blocking API 때문에 Reactor 시스템 전체가 Hang 걸릴 수도 있다. 이를 Reactor Meltdown이라 한다. blocking 코드를 위한 별도의 스레드풀을 만들고 해당 코드를 격리시키는 방법을 사용할 수 있다. 격리하는 메서드로 subscribeOn(), publishOn()이 있다.

## 참고
[[NHN FORWARD 2020] 내가 만든 WebFlux가 느렸던 이유](https://youtu.be/I0zMm6wIbRI)