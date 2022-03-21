# Spring MVC Request Lifecycle
Spring MVC의 요청 라이프사이클

## 이미지 자료

![image](https://docs.spring.io/spring-framework/docs/current/reference/html/images/mvc-context-hierarchy.png)

![image](https://docs.spring.io/spring-framework/docs/3.2.x/spring-framework-reference/html/images/mvc.png)

![image](https://i.stack.imgur.com/IjOIC.jpg)

## Spring MVC Request Lifecycle

사용자가 웹 애플리케이션으로 요청을 보낼 때 그 요청을 처리하는 워크플로우는 다음과 같다.

1. ```DispatcherServlet```은 요청을 받으면 적절한 ```Controller```를 선택하는 작업을 ```HandlerMapping```에 전달한다.
2. ```HandlerMapping```은 들어오는 요청 URL에 매핑되는 ```Controller```를 선택하고 ```DispatcherServlet```에 ```Controller```를 반환한다.
3. ```DispatcherServlet```은 ```Controller```를 실행하는 작업을 ```HandlerAdapter```로 전달한다.
4. ```HandlerAdapter```는 ```Controller``` 프로세스를 호출한다. ```HandlerAdapter```가 ```Controller```를 호출하면 요청을 가로채기 위해 ```Interceptor```가 호출될 수 있다.
5. ```Controller```는 비즈니스 로직을 실행하고 처리 결과를 ```Model```에 반영한다. 
6. ```Controller```는 ```View```의 논리적 이름을 ```HandlerAdapter```에 반환한다.
7. ```DispatcherServlet```은 그 이름에 해당하는 ```View```를 알아내는 작업을 ```ViewResolver```에 전달한다.
8. ```ViewResolver```는 해당 이름에 매핑된 ```View```를 반환한다.
9. ```DispatcherServlet```은 반환된 ```View```에 렌더링 프로세스를 전달한다.
10. ```View```는 ```Model``` 데이터를 렌더링하고 응답을 반환한다.

## DispatcherServlet
> ```DispatcherServlet```은 Request Lifecycle 중앙에서 모든 흐름의 제어를 담당한다.

사용자의 요청이 가장 먼저 도착하는 곳은 스프링의 DispatcherServlet이며 DispatcherServlet이 스프링 MVC의 핵심이다. 대부분의 자바 기반의 MVC 프레임워크와 마찬가지로 스프링 MVC도 요청을 프론트 컨트롤러 서블릿으로 보낸다. 프론트 컨트롤러는 요청 처리의 핵심을 다른 컴포넌트들에 위임해 실제로 처리하게 하는 웹 애플리케이션 패턴이다. 스프링 MVC에서는 DispatcherServlet이 프론트 컨트롤러에 해당한다.  

```text
DispatcherServlet -> FrameworkServlet -> HttpServletBean -> HttpServlet
```
DispatcherServlet은 부모 클래스에서 HttpServlet을 상속 받아서 사용하며 서블릿으로 동작한다. 스프링 부트는 DispatcherServlet을 서블릿으로 자동으로 등록하면서(DispatcherServlet을 서블릿으로 등록하면서 내장 WAS를 띄운다) 모든 경로("/")에 대해 매핑한다. 하위 어떤 경로를 호출하든 DispatcherServlet이 호출된다. 우리 만든 다른 서블릿은 어떻게 되나.. 더 자세한 경로가 우선순위가 높다. 그래서 기존에 등록한 서블릿도 동작한다. (DispatcherServlet이 우선순위가 더 낮을 것)  

서블릿이 호출되면 HttpServlet이 제공하는 service()가 호출된다. 스프링 MVC는 DispatcherServlet의 부모인 FrameworkServlet에서 service()를 오버라이드 해놨다. FrameworkServlet.service()를 시작으로 여러 메서드가 쭉쭉 호출되면서 DispacherServlet.doDispatch()가 호출된다. 이 메서드가 제일 중요하다. 

```java
//org.springframework.web.servlet.DispatcherServlet 축약된 코드!!!
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
	HttpServletRequest processedRequest = request;
	HandlerExecutionChain mappedHandler = null;
	ModelAndView mv = null;

	// 1. 핸들러 조회
	mappedHandler = getHandler(processedRequest); 
	if (mappedHandler == null) {
		noHandlerFound(processedRequest, response); // 없으면 404 NOT FOUND를 리턴한다.
		return; 
	}
	
	//2.핸들러 어댑터 조회 - 핸들러를 처리할 수 있는 어댑터를 찾아서 넘긴다
	HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler()); // 루프를 돌면서 찾고 반환한다. 없으면 예외 발생한다.
	
	// 3. 핸들러 어댑터 실행 -> 4. 핸들러 어댑터를 통해 핸들러 실행 -> 5. ModelAndView 반환
	mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
	
	processDispatchResult(processedRequest, response, mappedHandler, mv,dispatchException);
}

private void processDispatchResult(HttpServletRequest request, HttpServletResponse response, HandlerExecutionChain mappedHandler, ModelAndView mv, Exception exception) throws Exception {
	// 뷰 렌더링 호출
	render(mv, request, response);
}

protected void render(ModelAndView mv, HttpServletRequest request, HttpServletResponse response) throws Exception {
	View view;
	String viewName = mv.getViewName(); 
	
	//6. 뷰 리졸버를 통해서 뷰 찾기, 7.View 반환
	view = resolveViewName(viewName, mv.getModelInternal(), locale, request);
	
	// 8. 뷰 렌더링
	view.render(mv.getModelInternal(), request, response);
}
```


## HandlerMapping
> ```HandlerMapping```은 들어오는 요청의 속성과 조건을 기반으로 해당 요청을 처리할 개체를 선택한다.

일반적으로 애플리케이션에는 여러 개의 Controller가 존재한다. DispatcherServlet은 요청을 어느 Controller로 보낼지 알아내기 위해 HandlerMapping의 도움을 받는다. HandlerMapping은 요청에 담긴 URL을 참고해서 적합한 Controller를 DispatcherServlet에 반환한다.

## HandlerAdapter
> ```HandlerAdapter```는 들어오는 요청을 처리하는 개체를 실행한다.

DispatcherServlet은 HandlerAdapter에게 Contoller를 실행하도록 요청한다. Controller의 결과는 다시 HandlerAdapter를 거치고, HandlerAdapter가 그 결과를 ModelAndView로 변환해서 DispatcherSerlvet에 반환한다.

## Controller
> ```Controller```는 들어오는 http 요청을 적절한 메서드에 매핑하는 게이트 역할이다.

Controller는 적절한 메서드에서 비즈니스 로직을 실행하고 처리 결과를 Model로 설정하여 반환한다. Controller에서 직접 정보를 처리하지 않고 비즈니스 로직에 대한 책임을 하나 이상의 Service 객체에 위임한다.

## Model
> ```Model```은 컨트롤러에 의해 비즈니스 로직이 수행된 후 사용자에게 반환되어 브라우저에 표시될 정보다.

## Interceptor
> ```Interceptor```는 Controller로 가기 전과 Controller에서 나온 후에 요청을 가로챌 수 있다.

## View
> ```View```는 Model 정보를 렌더링하여 HTML처럼 사용자에게 익숙한 형식으로 보여준다.

## ViewResolver
> ```ViewResolver```는 논리적 뷰 이름을 기반으로 실제로 결과를 만들어 낼 뷰를 선택한다.

## 참고
https://docs.spring.io/spring-framework/docs/3.2.x/spring-framework-reference/html/mvc.html  
https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-config-interceptors  
스프링 인 액션 3판 7장  
https://en.wikipedia.org/wiki/Spring_Framework  