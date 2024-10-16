# Custom annotation
커스텀 어노테이션

## 커스텀 어노테이션
```java
@Override
public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) 
	throws Exception {...}
```

부모 클래스의 메서드를 오버라이딩하려면 자식 클래스의 메서드에 @Override 어노테이션을 적용해야 한다. 이 @Override는 아래의 코드로 구현된다.

```java
@Target({ElementType.METHOD})
@Retention(RetentionPolicy.SOURCE)
public @interface Override {
}
```

자바에서 제공하는 @Override 어노테이션처럼 개발자가 직접 어노테이션을 만들 수 있다. 이를 ```커스텀 어노테이션```이라 한다.

## 구현
```java
@Target({ElementType.METHOD, ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface CustomAnnotation {
}
```

어노테이션의 타입은 ```@interface```으로 한다. 그리고 ```@Target```과 ```@Retention```을 지정한다.

### @Target
> 어노테이션이 선언될 수 있는 위치를 지정한다.  

예를 들어, ```@Target(ElementType.METHOD)```로 지정하면 메서드에만 어노테이션을 달 수 있다. 다른 곳에 어노테이션을 적용할 경우 컴파일 에러가 발생한다. ```@Target``` 속성을 지정하지 않으면 default로 해당 어노테이션을 모든 곳에서 사용할 수 있게 된다.  

- ```TYPE``` : 타입(class, 어노테이션 타입을 포함한 interface, enum)에 선언할 수 있다.
- ```FIELD``` : enum 상수를 포함한 인스턴스 변수에 선언할 수 있다.
- ```METHOD``` : 메서드에 선언할 수 있다.
- ```PARAMETER``` : 파라미터에 선언할 수 있다.
- ```CONSTRUCTOR``` : 생성자에 선언할 수 있다.
- ```LOCAL_VARIABLE``` : 로컬 변수에 선언할 수 있다.
- ```ANNOTATION_TYPE``` : 다른 어노테이션 타입에 선언할 수 있다.
- ```PACKAGE``` : 패키지에 선언할 수 있다.
- ```TYPE_PARAMETER``` : 파라미터 타입에 선언할 수 있다.
- ```TYPE_USE``` : 모든 타입에 선언할 수 있다.
- ```MODULE``` : 모듈에 선언할 수 있다.

### @Retention
> 어노테이션이 유효한 시점을 지정한다.  

```@Retention``` 속성을 지정하지 않으면 default로 RetentionPolicy.CLASS가 선택된다.  

- ```SOURCE``` : 소스 코드까지만 유효하며 컴파일 시점에 컴파일러에 의해 제거된다.
- ```CLASS``` : 컴파일러에 의해 생성되는 클래스 파일에 기록되지만 런타임에 VM에 의해 참조될 수 없다.
- ```RUNTIME``` : 컴파일러에 의해 생성되는 클래스 파일에 기록되며, 런타임에 VM에 의해 참조될 수 있다. 참조하는 정보를 바탕으로 특정 로직을 수행할 수 있다.

### 그 외
@Inherited, @Documented 등의 어노테이션을 지정할 수 있다.

## 참고
https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/annotation/Annotation.html  
https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/annotation/ElementType.html  
https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/annotation/RetentionPolicy.html   
