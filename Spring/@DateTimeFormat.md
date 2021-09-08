# @DateTimeFormat
Spring에서 제공하며 필드나 메서드 파라미터에 선언하는 어노테이션

## 기능
문자열 데이터를 원하는 날짜 타입으로 파싱해준다. 

## 필드에서 사용하기
```java
@Setter
@Getter
@NoArgsConstructor
public class BreadDTO {
	private String name;

	@DateTimeFormat(pattern = "yyyy/MM/dd")
	private Date expiryDate;
}
```

'yyyy/MM/dd' 형식의 문자열이 입력되면 자동으로 Date 타입으로 변환된다. 

## 메서드 파라미터에서 사용하기

```java
@PostMapping("/weekly/{targetDate}")
public JsonResult method(@DateTimeFormat(pattern = "yyyyMMdd") @PathVariable("value") Date date) {
	//...
}
```

```java
@RequestMapping(value = "/daily/{targetDate}", method = {RequestMethod.POST, RequestMethod.GET}) 
public JsonResult method(@DateTimeFormat(pattern = "yyyy-MM-dd") @PathVariable(value = "targetDate") LocalDate targetDate) { 
	//...
}
```

## 제공되는 포맷
열거형 DateTimeFormat.ISO에는 자주 사용되는 포맷들이 들어 있다. ISO 패턴으로 설정하게 되면 @DateTimeFormat이 적용된 필드나 파라미터들은 [ISO 8601](https://ko.wikipedia.org/wiki/ISO_8601)에 따라 파싱된다.

- DATE : "yyyy-MM-dd", "2021-09-08"
- DATE_TIME : "yyyy-MM-dd'T'HH:mm:ss.SSSXXX", "2021-09-08T23:28:00.000-05:00"
- NONE 
- TIME : "HH:mm:ss.SSSXXX", "23:28:00.000-05:00"

> **'T'의 의미**  
> 'T' 자체는 어떤 의미를 가지지 않는다. [ISO 8601 혼합 일시 표현](https://ko.wikipedia.org/wiki/ISO_8601#%ED%98%BC%ED%95%A9_%EC%9D%BC%EC%8B%9C_%ED%91%9C%ED%98%84)에서 요구하는 분리자일 뿐이다.  

### ISO 포맷 적용하기
```java
@DateTimeFormat(iso = ISO.DATE)
private Date expiryDate;
```

```java
@RequestMapping(value = "/daily/{targetDate}", method = {RequestMethod.POST, RequestMethod.GET}) 
public JsonResult method(@DateTimeFormat(iso = ISO.DATE_TIME) @PathVariable(value = "targetDate") LocalDate targetDate) { 
	//...
}
```
## 참고
https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/format/annotation/DateTimeFormat.html  
https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/format/annotation/DateTimeFormat.ISO.html  
https://goodteacher.tistory.com/261  
https://stackoverflow.com/questions/29281935/what-exactly-does-the-t-and-z-mean-in-timestamp  