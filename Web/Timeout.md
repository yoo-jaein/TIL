# Timeout

## Connection Timeout

Connection timeout은 클라이언트와 서버 간의 연결(TCP 3-way handshake)이 지정된 시간 내에 맺어지지 않으면 발생한다. 일반적으로 원격 호스트가 응답하지 않는 경우에 발생한다. 자바 소켓 프로그래밍에서는 클라이언트 측에서 서버로 연결하기 위해 소켓 생성자가 호출되어 소켓 객체가 인스턴스화된다. 생성자는 원격 호스트 주소와 포트 번호를 인수로 받아 해당 호스트에 연결을 시도한다. 이 작업은 성공적으로 연결될 때까지 다른 모든 프로세스를 차단하는데, 특정 시간이 지난 후에도 연결에 성공하지 못하면 "Connection timed out" 메시지와 함께 java.net.ConnectionException이 발생한다.  

```text
java.net.ConnectException: Connection timed out
```

Connection timeout에는 다음과 같은 여러 가지 이유가 있을 수 있다.
- 잘못된 IP/DNS 이름/포트를 사용한 경우
- 원격 호스트가 종료된 경우
- 원격 호스트가 연결을 수락하지 않는 경우
- 원격 호스트를 사용할 수 없는 경우
- 인터넷 연결이 느린 경우
- 원격 호스트에 대한 포워딩 경로가 없는 경우
- 방화벽에서 보안상 이유로 특정 포트를 차단한 경우 

```java
WebClient webClient = WebClient
	.builder()
	.clientConnector(
		new ReactorClientHttpConnector(
			HttpClient.create()
				.option(ChannelOption.CONNECT_TIMEOUT_MILLIS, 10000) // 밀리초 단위, 10초
		)
	)
.build();
```

WebClient를 이용할 때 Connection timeout 시간을 설정하려면 HttpClient 인스턴스를 만들고 이를 clientConnector로 설정해야 한다. 위 코드에서 10초 이내에 클라이언트와 서버 연결이 완료되지 않으면 ConnectionTimeoutException이 발생한다. Netty는 Connection timeout을 기본적으로 30초로 설정한다. 

## Read Timeout

자바 소켓 프로그래밍에서 InputStream의 read() 메서드 호출은 소켓에서 데이터 바이트 읽기를 마칠 때까지 블록된다. 이 메서드는 소켓에서 최소 하나의 바이트를 읽을 때까지 계속 기다린다. 만약 이 대기 시간이 굉장히 길어지면 "Read timed out" 메시지와 함께 InterrupedIOException이 발생한다.  

이처럼 연결이 맺어진 이후, 클라이언트 측에서 서버가 응답하고 정보를 보내는 것을 더 이상 기다릴 수 없는 경우 Read timeout이 발생한다. 미리 설정된 타임아웃에 비해 서버에서 데이터를 처리하는 데 시간이 오래 걸리는 경우 발생할 수 있다.

```text
java.net.SocketTimeoutException: Read Timed out
```

```java
WebClient webClient = WebClient
	.builder()
		.clientConnector(
			new ReactorClientHttpConnector(HttpClient.create()
				.doOnConnected(connection -> connection
					.addHandler(new ReadTimeoutHandler(10)) // 초단위, 10초
			)
		)
	)
.build();
```
WebClient를 이용할 때 Read timeout 시간을 설정하려면 HttpClient 인스턴스를 만들고 doOnConnected 콜백 핸들러를 사용해야 한다. 그런 다음 HttpClient 인스턴스를 WebClient의 clientConnector로 설정해야 한다. 위 코드에서 연결이 설정된 후 10초 이내에 읽기 작업이 시작되지 않으면 ReadTimeoutException이 발생한다.

## Write Timeout
클라이언트가 서버로 패킷을 보낼 수 있는 timeout을 설정할 수 있다. 클라이언트와 서버가 connection은 맺어졌지만 설정된 timeout의 값 보다 데이터를 보내는(요청) 시간이 길어지면 Write Timeout이 발생하여 Exception이 떨어진다.

쓰기 작업을 완료할 수 없는 경우 Write timeout이 발생한다. 

```java
WebClient webClient = WebClient
	.builder()
		.clientConnector(
			new ReactorClientHttpConnector(HttpClient.create()
				.doOnConnected(connection -> connection
					.addHandler(new ReadTimeoutHandler(10)
					.addHandler(new WriteTimeoutHandler(10)) // 초단위, 10
			)
		)
	)
.build();
```
위 코드에서 연결이 설정된 후 10초 이내에 쓰기 작업이 완료되지 않으면 WriteTimeoutException이 발생한다. 

## Socket Timeout
클라이언트와 서버는 연결된 후에 여러 개의 패킷으로 통신한다. Socket timeout은 이렇게 지속적으로 들어오는 데이터의 흐름을 모니터링하는 데 사용된다. 지정된 제한 시간 동안 데이터가 들어오지 않으면 연결이 중단되거나 단절된 것으로 간주되어 Socket timeout이 발생한다.

## Reactive Timeout
응답 요청별로 timeout을 설정할 수도 있다. Spring WebFlux로 작업할 때 가장 기본적으로 설정할 수 있는 Reactive timeout은 Spring WebFlux가 기반으로 하는 Reactive Core API에 의해 부과된다. Mono와 Flux의 timeout() 메서드를 통해 제한 시간을 설정할 수 있다.

```java
WebClient.create(GET_STUDENTS_URL)
	.get()
	.retrieve()
	.bodyToFlux(Student.class)
	.timeout(Duration.ofSeconds(10));
```

위 코드는 10초 동안 항목이 도착하지 않으면 WebFlux에서 TimeoutException이 발생한다. Reactive timeout은 반응 시간이 초과된 것으로 고수준의 시간 초과를 의미한다. 세부적인 timeout을 추가하려면 Reactor Netty 등의 다른 옵션을 사용해야 한다. 
