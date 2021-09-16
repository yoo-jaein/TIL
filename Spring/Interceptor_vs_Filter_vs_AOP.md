# Interceptor vs Filter vs AOP

## Interceptor
- DispatcherServlet이 Controller를 호출하기 전과 후에 동작할 수 있다. 
- 스프링 컨텍스트 내부에 위치하기 때문에 스프링의 모든 빈 객체에 접근할 수 있다. 
- 파라미터로 HttpServletRequest, HttpServletResponse를 가진다. 
- Handler 역할을 하는 Controller로 가기 전에 가로채기 때문에 정식 명칭은 HandlerInterceptor이다. 
- preHandle, postHandle, afterCompletion

## Filter
- DispatcherServlet보다 바깥쪽에 위치한다. 
- 스프링 컨텍스트 바깥, 웹 애플리케이션에 위치하며 스프링과 무관하게 적용할 수 있다.
- DispatcherServlet 이전에 실행되어 Request와 Response를 거르고 정제한다. 
- web.xml에 등록해서 사용한다. 

## AOP
- Aspect-Oriented Programming, 관점 지향 프로그래밍 
- 비즈니스단의 공통 관심사를 처리한다. 
- Interceptor, Filter보다 실행 시점을 자유롭게 선택할 수 있다. 
- Before, Around, After, After Returning, After Throwing

## 참고
초보 웹 개발자를 위한 스프링5 프로그래밍 입문  
