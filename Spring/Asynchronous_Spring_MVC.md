# Asynchronous Spring MVC
Spring MVC로 비동기 프로그래밍하기

## Spring MVC의 논블로킹 IO
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
		for (int i = 0; i < 100; i++) {
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
