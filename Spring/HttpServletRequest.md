# HttpServletRequest
javax.servlet.http.HttpServletRequest

## HttpServletRequest
```HttpServletRequest```는 ServletRequest 인터페이스를 확장한 인터페이스다. Http의 Request 정보를 서블릿에 전달하기 위해 사용된다. 서블릿 컨테이너는 ```HttpServletRequest``` 객체를 생성하고 이를 서블릿의 서비스 메서드(doGet, doPost 등)에 인수로 전달한다. ```HttpServletRequest```를 통해 Request에 대한 ```헤더 정보```, ```URI```, ```URL```, 사용하는 ```포트```, ```보안 여부``` 등의 정보를 확인할 수 있다.

## 사용법
### URL 정보
```text
https://myhost.com:8080/people/list.do?lastname=Fox&age=30
```
- StringBuffer getRequestURL() : Query를 제외한 경로를 반환한다. (https://myhost:8080/people/list.do)
- String getRequestURI() : ContextPath와 ServletPath를 합친 경로를 반환한다. (/people/list.do)
- String getContextPath() : Context 경로. 웹 애플리케이션의 프로젝트 명을 반환한다. 
- String getServletPath() : Servlet 경로. ContextPath를 제외한 경로를 반환한다. (/people/list.do)
- String getQueryString() : Query 정보를 반환한다. (lastname=Fox&age=30)
- String getServerPort() : 포트 번호를 반환한다. (8080)
- String getServerName() : 도메인 주소를 반환한다. (myhost.com)

### 파라미터 정보
- Enumeration<String> getParameterNames()
- String[] getParameterValues(String name)
- String getParameter()

### 속성 정보
- Enumeration<String> getAttributeNames()
- Object getAttribute(String name)

### 헤더 정보
- Enumeration<String> getHeaders(String name)
- String getHeader(String name) : 지정된 요청 헤더의 값을 문자열로 반환한다. 이름이 같은 헤더가 여러 개인 경우 요청의 첫 번째 헤더를 반환한다. 

### 바디 정보
- ServletInputStream getInputStream()

### 쿠키 정보
- Cookie[] getCookies() : 클라이언트가 요청과 함께 보낸 모든 쿠키 객체를 담은 배열을 반환한다. 

### 세션 정보
- HttpSession getSession()
- boolean isRequestedSessionIdFromCookie()
- boolean isRequestedSessionIdFromURL()
- boolean isRequestedSessionIdValid()

### 클라이언트 정보
- String getRemoteAddr()
- String getRemoteHost()
- int getRemotePort()

### 서버 정보
- String getLocalAddr()
- String getLocalName()
- int getLocalPort()

### 기타 정보
- boolean isSecure()
- String getAuthType() : 서블릿을 보호하기 위해 사용되는 인증 스키마의 이름을 반환한다.
- Locale getLocale()
- String getScheme() : http, https, ftp 등 프로토콜의 이름을 반환한다.
- String getProtocol() : 요청이 사용하는 프로토콜과 버전을 반환한다.
- String getMethod() : GET, POST 등 사용한 HTTP 메서드의 이름을 반환한다.

## 활용 : HandlerInterceptor
```java
public interface HandlerInterceptor {
	default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) 
			throws Exception { 
		return true; 
	}

	default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, 
		@Nullable ModelAndView modelAndView) throws Exception {
	}

	default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler,
		@Nullable Exception ex) throws Exception {
	}
}
```
스프링에서 인터셉터를 구현할 때 사용하는 ```HandlerInterceptor``` 인터페이스의 preHandle(), postHandle(), afterCompletion()에 ```HttpServletRequest```가 파라미터로 포함되어 있다. 따라서 컨트롤러 실행 전, 후, 뷰 렌더링 후 시점에 Request 정보에 접근할 수 있다. 

## 참고
https://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequest.html  
https://stackoverflow.com/questions/17241532/get-root-domain-from-request  
https://d8040.tistory.com/188  