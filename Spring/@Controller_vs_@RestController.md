# @Controller vs @RestController

## @Controller
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Controller {

	@AliasFor(annotation = Component.class)
	String value() default "";
}
```
@Controller가 붙은 클래스는 컨트롤러 역할을 한다. 이 어노테이션은 @Component의 특별한 형태로, @RequestMapping을 기반으로 하는 메서드와 함께 사용된다. 

```java
@Controller
@RequiredArgsConstructor
public class ItemController {

	private final ItemService itemService;

	@GetMapping("/items/new")
	public String createForm(Model model) {
		model.addAttribute("form", new BookForm());
		return "items/createItemForm";
	}
}
```

```java
@Controller
@RequiredArgsConstructor
public class ItemController {

	private final ItemService itemService;

	@GetMapping("/items")
	@ResponseBody
	public List<Item> getItems() {
		return itemService.getItems();
	}
}
```

서버가 클라이언트에게 파일을 리턴해줄 때는 @Controller 어노테이션을 사용하고, 데이터를 리턴해줄 때는 @RestController어노테이션을 사용한다. 그런데 만약 @Controller가 작성된 자바파일에서 데이터를 클라이언트에게 리턴해주고 싶을 땐 어떻게 해야할까? 이때 지금 다루는 @ResponseBody 어노테이션을 사용한다.  

@ResponseBody는 간단히 말해 @Controller 어노테이션이 달린 자바파일에서 클라이언트에게 데이터를 리턴해줄 때 사용하는 어노테이션이다.

그럼 @ResposeBody를 제거하면 어떤 결과가 나올까? @ResponseBody가 없으면 파일을 리턴해주기 때문에 템플릿이 존재하지 않으므로 당연히 페이지를 찾을 수 없는 404에러가 뜰것이다. 

```text
{
    "timestamp": "2022-04-10T14:10:56.769+00:00",
    "status": 404,
    "error": "Not Found",
    "path": "/int/v2"
}
```


간단히 정리하면 @ResponseBody가 없으면 return값이 파일(requsetDispatcher)인데 있으면 return값이 데이터(printWriter)가 되므로 @Controller 어노테이션이 작성된 자바파일에서 데이터를 리턴하고자 할때는 @ResponseBody를 사용하면된다.

## @RestController
RestController에 대해서 알아보려면 먼저 REST방식이 무엇인지 알아야한다.

## REST
REST는 'Representational State Transfer' 의 약어로 하나의 URI는 하나의 고유한 Resource를 대표하도록 설계된다는 개념이다. 이 말은 다른 말로 'URI와 HTTP메소드를 이용해 객체화된 서비스에 접근한다고 말하는 것'이 라고 말할 수도 있는데
이 편이 더 쉽게 이해할 수도 있다.

REST API는 외부에서 특정 URI를 통해서 사용자가 원하는 정보를 제공하는 방식이다. 최근에 Open API에서 많이 사용되면서 REST 방식을 제공되는 외부 연결 URI를 REST API라고 하고, REST 방식의 서비스 제공이 가능한 것을 'Restful' 하다고 표현한다.

## REST 특징
- Stateless : REST의 가장 큰 강점 중 하나이다. 이전, 이후의 대한 직접적인 정보가 필요없이 직관적인 오브젝트에의 접근으로 서비스를 처리한다. 쿠키/세션이 필요없다.
- URI를 이용 : REST는 모든 유일한 객체에 대해 유일하고 직관적인 URI을 통해 접근하도록 한다.
- HTTP 메소드를 사용 : REST는 HTTP에서 제공하는 GET, PUT, POST, DELETE 4개의 메소드를 이용해서 서비스를 제공한다. 이것이 REST의 단점이 되기도 하는데, 4개의 메소드가 DB의 CRUD와 같은 기능을 하므로 이러한 유형이 아닌 작업에 대해서는 어떻게 처리해야되는지 모호한 부분이다.

## @RestController
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Controller
@ResponseBody
public @interface RestController {
	
	@AliasFor(annotation = Controller.class)
	String value() default "";
}
```
@Controller와 @ResponseBody로 주석이 달린 편리한 주석.
이 주석을 포함하는 유형은 @RequestMapping 메서드가 기본적으로 @ResponseBody 의미 체계를 가정하는 컨트롤러로 처리됩니다.
참고: MVC Java 구성 및 MVC 네임스페이스의 기본값인 RequestMappingHandlerMapping-RequestMappingHandlerAdapter 쌍과 같이 적절한 HandlerMapping-HandlerAdapter 쌍이 구성된 경우 @RestController가 처리됩니다.

```java
@RestController
@RequiredArgsConstructor
public class MemberApiController {

	private final MemberService memberService;

	@GetMapping("/api/v1/members")
	public List<Member> members() {
		return memberService.findMembers();
	}
}
```

스프링에서 Restful하게 작성하기 위해서는 @Controller 내에서 @ResponseBody를 사용하는 것과 @RestController을 사용하는 방법이 있다. 스프링은 3버전부터 @ResponseBody 애노테이션을 지원하면서 본격적으로 REST 방식의 처리를 지원하였다. 스프링 4에 들어와서 @RestController가 본격적으로 소개되었다. 스프링 4버전부터 지원되는 '@RestController' 애노테이션의 경우 기존의 특정한 JSP와 같은 뷰를 만들어 내는 것이 목적이 아닌 REST방식의 데이터 처리를 위해서 사용되는 애노테이션이다.

@ResponseBody
메소드에서 @ResponseBody 어노테이션을 사용하면 Spring은 반환 값을 변환하여 HTTP Response 에 자동으로 씁니다. Controller 클래스의 각 메소드에는 @ResponseBody 어노테이션이 있어야합니다.

@RestController
Spring 4.0은 @Controller와 @ResponseBody 을 합쳐놓은것 이상의 역할을 수행하는@RestController를 추가했습니다. 컨트롤러 클래스에 @RestController 어노테이션을 작성함으로써 더 이상 @ResponseBody를 모든 요청 매핑 메소드에 추가 할 필요가 없습니다. @ResponseBody 어노테이션은 이제 기본으로 작동..!

## @Controller vs @RestController
스프링 MVC의 컨트롤러와 Restful 웹 서비스 컨트롤러의 주요 차이점은 HTTP 응답 바디가 생성되는 방식이다. 스프링 MVC 컨트롤러의 경우 View 기술을 사용하지만, Restful 웹 서비스 컨트롤러는 객체를 반환하기만 하면 객체 데이터는 JSON/XML형식의 HTTP응답에 직접 작성하게 된다. 따라서 REST방식을 사용하기위해 @Controller의 경우 컨트롤러 클래스의 각 메소드에는 @ResponseBody 애노테이션이 있어야한다. 그에 반해 @RestController는 @Controller, @ResponseBody를 합쳐놓은 것 이상의 역할을 한다. 컨트롤러 클래스에 @RestController 애노테이션을 작성함으로써 더 이상 각 메소드 @ResponseBody 애노테이션을 작성할 필요가 없게 된다.

정리해보면 @RestController는 View가 필요없는 REST방식에서 주로 사용되고 @ResponseBody를 포함하고 있다. @Controller는 View와 REST방식을 동시에 사용가능하지만 REST방식은 @ResponseBody를 사용해야한다.  

