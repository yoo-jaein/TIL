# Handling Exception
스프링 예외 처리하기

1. 어디서 처리할까?
2. 어떻게 처리할까?

## 1. 어디서 처리할까?
### 1) try-catch 문
- 장점
  - 예외가 명시적이다.
- 단점
  - 예외의 종류가 다양하거나 예외를 잡는 메서드가 많아질 경우 코드가 매우 지저분해진다. 따라서 우리의 목표는 가능한 예외를 명시적으로 처리하지 않는 것으로 하자.

```java
@GetMapping
public ResponseEntity<List<DocumentResponse>> getAllDocuments() {
	try {
		return ResponseEntity.ok(documentService.findAllDocuments());
	} catch (...) {
		//...
	}
}
```

### 2) 컨트롤러 레벨의 @ExceptionHandler
```java
// DocumentController
@ExceptionHandler({ DocumentException.class, ReplyException.class })
public void handleException() {
	//...
}
```

컨트롤러 클래스에 @ExceptionHandler 메서드를 작성하여 해당 컨트롤러의 메서드에서 발생한 예외를 처리할 수 있다. 이 방법은 전체 애플리케이션에 대한 예외 처리를 하는 것이 아니라 특정 컨트롤러에 대해서만 예외 처리를 한다. 

### 3) HandlerExceptionResolver

스프링에서 제공하는 Resolver를 쓴다. SimpleMappingExceptionResolver의 인스턴스를 활용한다.

### 4) @ControllerAdvice (Global Exception Handling)
```java
@Slf4j
@RestControllerAdvice
public class CommonRestExceptionHandler {
	@ExceptionHandler(Exception.class) 
	public ResponseEntity<Object> handleException(Exception e) {
		log.error("defaultExceptionHandler", e);
		//...
	}
}
```

@ControllerAdvice는 전역으로 @ExceptionHandler를 사용할 수 있게 해준다. 그래서 이 방법을 Global Exception Handling이라 부른다.

- 장점
  - 컨트롤러에서 예외를 throw할 필요 없다. handler에 예외를 위한 메서드를 작성하면 그 메서드에서 자동으로 처리해준다.
  - try-catch 문을 사용할 필요 없이 코드가 깔끔해진다.
  - 대부분의 예외를 핸들러 클래스에서 처리하므로 코드 응집도가 높아진다.

위의 1~4) 방법을 섞어서 사용할 수도 있다. 그러나 하나의 애플리케이션에서 여러 옵션을 혼합할 때는 주의해야 한다. 동일한 예외가 여러 가지 방식으로 처리될 수 있는 경우, 처리 순서에 따라 원하는 동작을 얻지 못할 수 있다. 예를 들어, 컨트롤러의 @ExceptionHandler 메서드는 @ControllerAdvice 인스턴스의 메서드보다 항상 먼저 선택된다.

## 2. 어떻게 처리할까?
### HTTP Status Code 이용하기

일반적으로 웹 요청을 처리할 때 처리되지 않은 예외가 발생하면 서버가 HTTP 500 응답을 반환한다. 이렇듯 [HTTP Status Code](https://github.com/yoo-jaein/TIL/blob/main/Web/HTTP_Status_Code.md)를 예외 처리에 활용할 수 있다.  

### @ResponseStatus
```java
@ResponseStatus(value=HttpStatus.NOT_FOUND, reason="Order가 존재하지 않습니다.")  // 404 NOT FOUND
public class OrderNotFoundException extends RuntimeException {
	// ...
}
```

직접 작성하는 모든 예외에는 @ResponseStatus 어노테이션을 달 수 있다. @ResponseStatus를 이용하면 HTTP 상태 코드와 사유를 명시할 수 있다. @ResponseStatus가 달린 예외가 컨트롤러 메서드에서 던져지고 다른 곳에서 처리되지 않으면 지정된 상태 코드로 응답이 반환된다.

```java
@GetMapping("/orders/{id}")
public String showOrder(@PathVariable("id") long id, Model model) {
	Order order = orderRepository.findOrderById(id);
	if (order == null) throw new OrderNotFoundException(id);
	//...
}
```

```java
void sendError(int sc, String msg) throws IOException
```

실패하여 reason을 응답에 설정할 때 HttpServletResponse의 sendError() 메서드가 사용되는데, 이 때 응답이 완료(commit)된 것으로 간주되기 때문에 이후에 수정할 수 없다. @ResponseStatus는 하나의 Exception에 대해 하나의 메시지만 표현할 수 있기 때문에 활용도가 떨어진다. 

### ResponseStatusException

```java
Order order = orderRepository.findOrderById(id);
if (order == null)
	throw new ResponseStatusException(HttpStatus.NOT_FOUND);
```

이후 스프링 5에 ResponseStatusException 예외 클래스가 등장한다. @ResponseStatus처럼 ResponseStatusException에 HttpStatus와 사유(String reason), 원인(Throwable cause)를 명시할 수 있다.

```java
throw new ResponseStatusException(HttpStatus.NOT_FOUND, String.format("Document not found: %s", documentId));
```

그러나 이 방법은 reason에 문자열을 전달하면서 오타가 발생할 수 있으므로 추천되지 않는다. 

```java
try {
	//...
} catch (DocumentException e) {
	throw new ResponseStatusException(HttpStatus.NOT_FOUND, e.getMessage(), e);
}
```

특정 예외로부터 ResponseStatusException를 구성할 수 있지만, try-catch 문을 써야 하므로 코드 가독성 측면에서 좋지 않다.

### REST API에서의 일반적 예외 처리
REST API에서는 일반적으로 Global Exception Handling 전략을 기반으로 HTTP Status Code과 커스텀 응답 코드를 활용한다. 커스텀 응답 코드를 사용할 경우 문서화가 필수적이며 API 클라이언트 측에서 응답 코드에 맞게 핸들링해줘야 한다.  

먼저 REST API에서 HTTP Status Code를 활용하는 다양한 케이스를 살펴보자. 이 중에 정답은 없으며 클라이언트의 성격, 애플리케이션의 특성, 개발자의 취향에 따라 선택한다. 
- 응답이 반드시 있어야 하는 API에서 해당되는 데이터가 없다면 에러 코드 404 NOT FOUND를 보내는 케이스.
  - 단건 조회(/document/3) API에서 요청 id에 해당하는 document가 없으며 클라이언트가 존재하지 않는 URL에 접근했다는 의미로 404 NOT FOUND를 보냄.
- 데이터가 있을 수도 있고 없을 수도 있는 API(예: 리스트를 반환하는 검색용 API)에서 검색 결과가 없을 때 요청에 대해 서버에서 정상적으로 처리해줬다는 의미로 200 SUCCESS를 보내거나 204 NO CONTENT를 보내는 케이스.
  - 만약 응답의 리스트가 비어있는게 오류라고 생각하면 4XX 코드를 보낼 수도 있음. 다만 일반적으로는 서버에서 정상적으로 처리되었다는 의미를 더 강조하여 2XX 코드를 씀.
- 모든 처리에 대해 항상 200 SUCCESS를 보내는 케이스.
  - HTTP는 전송 계층일 뿐 서버로부터 무언가 응답이 왔으면 성공으로 간주하는 것.

### 1) CustomResponseEntity 구현하기
- 모든 처리에 대해 항상 HTTP 200 SUCCESS를 전달한다. 

```java
@Getter
public enum Result {
	OK(0, "정상 처리"),
	FAIL(-1, "실패"),

	DOCUMENT_NOT_EXISTS(101, "존재하지 않는 문서입니다."),

	ONLY_AUTHOR_CAN_UPDATE(201, "글쓴이만 문서를 수정할 수 있습니다."),
	ONLY_AUTHOR_CAN_DELETE(202, "글쓴이만 문서를 삭제할 수 있습니다.");

	private final int code;
	private final String message;

	Result(final int code, final String message) {
		this.code = code;
		this.message = message;
	}
}
```

REST API의 응답으로 ResponseEntity를 반환하는데, 모든 처리에 대해 200 SUCCESS를 전달하는 경우 응답 데이터의 의미를 명확히 알 수 없다. 따라서 커스텀 응답 코드를 만들어주고 응답에 함께 내려줘야 한다. 이 때 응답 코드는 애플리케이션에서 발생할 수 있는 여러 가지 상황을 표현할 수 있어야 한다. int형 code를 상황별로 유니크하게 구성하고 String형 message로 그 구체적인 상황을 전달해주어 클라이언트에서 각 코드를 쉽게 구분하고 이해할 수 있게 해주자.  

```java
@Getter
public enum Result {
	OK(HttpStatus.OK, 0, "정상 처리"),
	;//...
	
	private final HttpStatus httpStatus;
	private final int code;
	private final String message;
}
```

상황에 따라 Result의 필드로 HttpStatus를 추가하는 것도 좋은 방법이다.

```java
@Getter
@Setter
public class DocumentException extends RuntimeException {
	private final Result result;

	public DocumentException(Result result) {
		this.result = result;
	}
}
```

예외 클래스의 필드로 Result를 포함시킨다.

```java
@Getter
@Setter
public class CustomResponseEntity<T> {
	private int code;
	private String message;
	private T data;

	public static <T> CustomResponseEntity<T> success(T data) {
		return CustomResponseEntity.<T>builder()
			.code(Result.OK.getCode())
			.message(Result.OK.getMessage())
			.data(data)
			.build();
	}

	public static <T> CustomResponseEntity<T> fail(Result result) {
		return CustomResponseEntity.<T>builder()
			.code(Result.OK.getCode())
			.message(Result.FAIL.getMessage())
			.data(data)
			.build();
	}

	@Builder
	public CustomResponseEntity(int code, String message, T data) {
		this.code = code;
		this.message = message;
		this.data = data;
	}
}
```

ResponseEntity 대신 사용할 CustomResponseEntity를 만들고 Result의 정보를 code와 message에 각각 담아준다.

```java
@Slf4j
@RestControllerAdvice
public class CommonRestExceptionHandler {

	@ResponseBody
	@ExceptionHandler(Exception.class)
	public CustomResponseEntity<String> handleException(Exception e) {
		log.error("defaultExceptionHandler", e);
		return CustomResponseEntity.fail(Result.FAIL, e.getMessage());
	}

	@ResponseBody
	@ExceptionHandler(DocumentException.class)
	public CustomResponseEntity<String> handleDocumentException(DocumentException e) {
		log.error("documentExceptionHandler", e);
		return CustomResponseEntity.fail(e.getResult(), e.getDebug()); 
	}
}
```

@RestControllerAdvice 혹은 @ControllerAdvice 어노테이션을 사용하여 예외를 처리할 Handler를 만든다. 컨트롤러에서 발생하는 모든 DocumentException은 handleDocumentException()에서 처리하고 그 외 Exception은 handleException()에서 처리한다. 

```java
// DocumentController
@GetMapping("/{documentId}")
public CustomResponseEntity<DocumentResponse> getDocument(@PathVariable Long documentId) {
	return CustomResponseEntity.success(documentService.getDocument(documentId));
}
```

```java
// DocumentService
public DocumentResponse getDocument(Long documentId) {
	Document document = documentRepository.findById(documentId)
		.orElseThrow(() -> new DocumentException(Result.DOCUMENT_NOT_EXISTS));
	//...
```

만약 documentId에 해당하는 문서가 없다면 DocumentException이 던져진다. 그러면 handleDocumentException()에서 그 예외를 처리하고 CustomResponseEntity를 만들어 반환한다.

### 2) ErrorResponse 구현하고 ResponseEntity 사용하기
- CustomResponseEntity를 만들지 않고 기존의 ResponseEntity를 그대로 활용
- 특정 예외 상황에 대해 HTTP Status Code를 다르게 구성

```java
@Getter
public enum Result {
	OK(0, "정상 처리"),
	FAIL(-1, "실패"),

	DOCUMENT_NOT_EXISTS(101, "존재하지 않는 문서입니다."),

	ONLY_AUTHOR_CAN_UPDATE(201, "글쓴이만 문서를 수정할 수 있습니다."),
	ONLY_AUTHOR_CAN_DELETE(202, "글쓴이만 문서를 삭제할 수 있습니다.");

	private final int code;
	private final String message;

	Result(final int code, final String message) {
		this.code = code;
		this.message = message;
	}
}
```

```java
@Getter
@Setter
public class DocumentException extends RuntimeException {
	private final Result result;

	public DocumentException(Result result) {
		this.result = result;
	}
}
```

```java
@Getter
public class ErrorResponse {
	private final int code;
	private final String message;
	//...

	public ErrorResponse(int code, String message) {
		this.code = code;
		this.message = message;
	}
}
```

이 방법에서는 예외 정보를 담을 ErrorResponse를 만든다.

```java
@GetMapping("/{documentId}")
public ResponseEntity<DocumentResponse> getDocument(@PathVariable Long documentId) {
	return ResponseEntity.ok(documentService.getDocument(documentId));
}
```

```java
@Slf4j
@RestControllerAdvice
public class CommonRestExceptionHandler {

	@ResponseBody
	@ExceptionHandler(Exception.class)
	public ResponseEntity<ErrorResponse> handleException(Exception e) {
		log.error("defaultExceptionHandler", e);
		ErrorResponse response = new ErrorResponse(e.getMessage());
		return new ResponseEntity<>(response, HttpStatus.BAD_REQUEST);
	}
	
	@ResponseBody 
	@ExceptionHandler(DocumentException.class) 
	public ResponseEntity<ErrorResponse> handleDocumentException(DocumentException e) {
		log.error("documentExceptionHandler", e);
		ErrorResponse response = new ErrorResponse(e.getCode(), e.getMessage());
		return new ResponseEntity<>(response, HttpStatus.NOT_FOUND);
	}
}
```

예외를 핸들링할 때 ErrorResponse를 생성하고 ResponseEntity로 래핑한 뒤 반환한다. 이 방법을 사용하면 예외 상황마다 HTTP Status Code를 다르게 구성할 수 있다. 예를 들어 단건 조회(/document/{id})에서 id에 해당하는 문서가 없을 경우 HTTP 404 NOT FOUND 코드를 내려줄 수 있다.

## 정리
- 컨트롤러별 예외 처리가 필요한 경우 컨트롤러에 @ExceptionHandler 메서드를 추가한다.
- 보편적인 예외 처리 방법은 Global Exception Handling 전략을 사용하는 것이다.
- 커스텀 응답 코드를 구성하는 경우 문서화를 해야 한다.

## 참고
https://spring.io/blog/2013/11/01/exception-handling-in-spring-mvc  
https://www.baeldung.com/exception-handling-for-rest-with-spring  
https://cheese10yun.github.io/spring-guide-exception/  
