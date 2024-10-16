# Jackson

## Jackson
Jackson은 자바의 Json 라이브러리다. 주로 자바 객체를 Json 문자열로 변환하거나 Json 문자열을 자바 객체로 변환할 때 사용한다. Json 외에도 XML, YAML 등 다양한 형식의 데이터를 지원한다.

## @JsonInclude: Null 값인 필드 제외하기
```java
@Setter
@Getter
@JsonInclude(Include.NON_NULL)
public class Student {
	private String name;
	private int grade;
	private boolean graduated;
	private School school;
}
```
- 클래스 단위로 설정하기

```java
@Setter
@Getter
public class Student {
	@JsonInclude(Include.NON_NULL)
	private String name;
	private int grade;
	private boolean graduated;
	@JsonInclude(Include.NON_NULL)
	private School school;
}
```
- 필드 단위로 설정하기

```java
ObjectMapper mapper = new ObjectMapper();
mapper.setSerializationInclusion(Include.NON_NULL);
```
- 매퍼 단위로 설정하기. 해당 매퍼로 직렬화된 모든 클래스들에 적용된다.

## @JsonProperty: Json으로 변환했을 때 필드 이름 변경하기
```java
@Setter
@Getter
public class Student {
	@JsonProperty("myName")
	private String name;
	private int grade;
	private boolean graduated;
	private School school;
}
```
```json
{
  "myName" : "kim",
  "grade" : 1,
  "graduated" : false,
  "School" : {
    ...
  }
}
```

## 참고
https://www.baeldung.com/jackson  
https://junho85.pe.kr/1626  