# HttpServletRequest
javax.servlet.http.HttpServletRequest

## HttpServletRequest
```HttpServletRequest```는 ServletRequest 인터페이스를 확장한 인터페이스다. Http의 Request 정보를 서블릿에 전달하기 위해 사용된다. 서블릿 컨테이너는 ```HttpServletRequest``` 객체를 생성하고 이를 서블릿의 서비스 메서드(doGet, doPost 등)에 인수로 전달한다. ```HttpServletRequest```를 통해 Request에 대한 ```헤더 정보```, ```URI```, ```URL```, 사용하는 ```포트```, ```보안 여부``` 등의 정보를 확인할 수 있다.

## 사용법
### URL 정보
- getRequestURL()
- getRequestURI()
- getContextPath()
- getServletPath()
- getQueryString()
- getServerPort()
- getServerName()

### 파라미터 정보
- getParameterNames()
- getParameter()

### 세션 정보
- getRequestedSessionId()
- getSession()
- isRequestedSessionIdFromCookie()
- isRequestedSessionIdFromURL()
- isRequestedSessionIdValid()

### 클라이언트 정보
- getRemoteAddr()
- getRemoteHost()
- getRemotePort()

### 서버 정보
- getLocalAddr()
- getLocalName()
- getLocalPort()

### 기타 정보
- isSecure()
- getLocale()
- getScheme()
- getProtocol()
- getMethod()

## 활용 : HandlerInterceptor
```java
public interface HandlerInterceptor {
	default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) 
			throws Exception { 
		return true; 
	}

	default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable ModelAndView modelAndView) throws Exception {
	}

	default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler,
		@Nullable Exception ex) throws Exception {
	}
}
```
스프링에서 인터셉터를 구현할 때 사용하는 ```HandlerInterceptor``` 인터페이스의 preHandle(), postHandle(), afterCompletion()에 ```HttpServletRequest```가 파라미터로 포함되어 있다. 따라서 컨트롤러 실행 전, 후, 뷰 렌더링 후 시점에 Request 정보에 접근할 수 있다. 

## 참고
https://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequest.html  
