# Inversion of Control with Dependency Injection
헷갈리기 쉬운 IoC와 DI

## IoC
IoC(Inversion of Control, 제어의 역전)은 객체나 프로그램 일부에 대한 컨트롤을 컨테이너 또는 프레임워크에게 넘긴다는 소프트웨어 엔지니어링 방법이다. 먼저 IoC가 적용되지 않은 코드를 살펴 보자.

```java
public class MemberProgram {
	public static void main(String[] args) {
		Member member1 = new Member("member1");
		System.out.println("member1.getName() = " + member1.getName());
	}
}

public class Member {
	private String name;

	public Member(String name) {
		this.name = name;
	}

	public String getName() {
		return this.name;
	}
}
```
자바로 프로그래밍할 때 일반적으로 개발자는 변수를 하나 선언하고 원하는 객체의 인스턴스로 해당 변수를 초기화한다. 위 코드는 제어가 역전된 것이 아니다. 개발자가 직접 new 키워드를 통해 Member의 생성자를 호출하며 객체를 컨트롤하고 있기 때문이다.  

IoC가 적용된 코드를 보기 전에, 먼저 IoC의 몇 가지 컨셉을 잡고 가자.
- '객체'의 '제어가 역전'된다는 것은 개발자가 아니라 다른 누군가가 객체를 제어한다는 뜻이다.
- IoC는 스프링에 한정적인 개념이 아니라 소프트웨어 프레임워크에서 제너럴하게 사용되는 개념이다.
- IoC가 애플리케이션에 적용되어 있다고 해도, 그 애플리케이션의 모든 객체에 IoC가 적용되었다는 의미는 아니다. 

```java
public class MemberProgram {
	private static IoCContainer ioCContainer; // 대략 어딘가에 IoCContainer가 있다고 가정하자
	
	public static void main(String[] args) {
		Member member1 = new Member("member1");
		System.out.println("member1.getName() = " + member1.getName());
		
		Team team1 = ioCContainer.getInstance(Team.class);
		System.out.println("team1.getName() = " + team1.getName());
	}
}

public class Member { ... }

public class Team {
	private String name;
	
	public Team(String name) {
		this.name = name;
	}
	
	public String getName() {
		return this.name;
	}
}
```
team1 변수를 초기화할 때 new 키워드를 통해 Team의 생성자를 호출하는 것이 아니라 iocContainer로부터 인스턴스를 받아오고 있다. member1 변수와 다르게 team1 변수를 초기화할 때 IoC가 적용된 것이다. 만약 개발자가 스프링 프레임워크를 사용하고 있다면 실제 코드는 이렇게 생길 것이다.

```java
team1 = context.getBean(Team.class);
```

## IoC의 장점
new 키워드로 생성자를 호출하는 것보다 복잡해보이는데 IoC를 적용했을 때의 장점은 무엇일까? 우리는 지금 단순히 2개의 객체만 생성하는 프로그램을 작성했지만 실제 애플리케이션에는 수많은 객체들이 존재한다. 당연히 모든 객체들은 코드 전체에서 매우 복잡한 종속성 그래프를 만들고 있을 것이다. IoC를 사용하면 IoC 컨테이너에서 객체를 관리해주기 때문에 개발자의 부담을 덜 수 있다. 만약 스프링을 사용한다면 개발자는 빈 객체의 생성, 구성, 소멸과 같은 라이프사이클을 스프링 컨테이너에게 맡기고 빈 객체가 수행할 작업만 작성하면 된다.  

다만, 모든 객체를 빈 객체로 만드는 것은 바람직하지 않다. 모든 객체를 빈 객체로 만드는 경우 애플리케이션을 로드할 때 성능이 저하될 수 있다. 일반적으로 스프링 빈 객체를 선정할 때 다음의 규칙을 적용한다.
- Controller, Service, Repository 계층은 스프링 빈으로 구성한다.
- 엔티티 클래스(Model)와 DTO, VO는 스프링 빈이 아닌 POJO로 구현한다. 
- Exceptions, enums도 스프링 빈으로 구성하지 않는다.

## IoC를 구현하는 방법
IoC는 다양한 매커니즘을 통해 달성할 수 있다.
- 전략 디자인 패턴(Strategy Pattern)
- 서비스 로케이터 패턴(Service Locator Pattern)
- 팩토리 패턴(Factory Pattern)
- 의존성 주입(Dependency Injection)

## DI
DI(Dependency Injection, 의존성 주입)은 IoC를 구현하기 위한 패턴 중 하나로, 클래스 외부에서 종속된 객체를 만들고 다양한 방법으로 해당 객체를 그 클래스에 제공하는 것이다. 객체 지향 프로그래밍에서 자주 사용된다. 스프링의 [IoC 컨테이너](https://github.com/yoo-jaein/TIL/blob/main/Spring/Spring_IoC_Container.md)인 BeanFactory에서 DI를 사용하고 있다. 먼저 DI가 적용되지 않은 코드를 살펴 보자.

```java
public class Member {
	private Team team;
	private String name;
	
	public Member() {
		this.team = new Team();
		this.name = "member";
	}
}

public class Team {
	private String name;
	
	public Team() {
		this.name = "team";
	}
}
```
DI 없이 객체의 종속성을 만들었다. Member 클래스의 생성자 코드에서 Team 객체를 직접 인스턴스화하고 있다. 이제 DI를 사용한 코드를 보자.

```java
public class Member {
	private Team team;
	private String name;
	
	public Member(Team team, String name) {
		this.team = team;
		this.name = name;
	}
}

public class Team {
	private String name;
	
	public Team(String name) {
		this.name = name;
	}
}
```
Member 클래스의 생성자에서 인자로 들어오는 Team 객체를 Member 클래스의 team 필드에 연결하고 있다. 이렇게 특정 객체에 필요한 다른 객체를 외부로부터 전달받아서 연결하기 때문에 '의존성을 주입한다'고 한다.

## DI의 장점
종속성을 쉽게 교체할 수 있다. 자바에서는 인터페이스나 추상 클래스를 이용해서 의존적인 객체의 관계를 유연하게 처리한다.
```java

```

## DI를 구현하는 방법
DI는 다음의 방법으로 구현할 수 있다.
- 생성자 주입
- setter 주입

### 1. 생성자 주입
```java
public class Member {
	private Team team;
	private String name;
	
	public Member(Team team, String name) {
		this.team = team;
		this.name = name;
	}
}
```
생성자로 객체를 주입하는 방법이다.
- 장점 : 객체를 생성하면서 모든 의존성을 주입받기 때문에 언제나 일관된 상태를 보장한다.
- 단점 : setter 메서드가 없다면 의존하는 객체를 변경할 수 없다. 또한, 순환 종속성 문제가 발생할 수 있다. 순환 종속성 문제는 잠시 뒤에 알아보자.

### 2. setter 주입
```java
public class Member {
	private Team team;
	private String name;
	
	public void setTeam(Team team) {
		this.team = team;
	}
}
```
setter로 객체를 주입하는 방법이다. 
- 장점 : 주입된 객체를 변경할 수 있다. 
- 단점 : setter를 통해 의존하는 객체가 주입되기 전까지 그 객체는 불완전한 상태다. 합리적인 기본값이 할당되지 않은 경우, 이 클래스의 종속성에 접근하는 모든 코드에서 null 검사를 수행해야 한다.  

## 스프링에서 DI를 구현하는 방법
[스프링 공식 문서](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-dependencies)에서도 생성자 주입과 setter 주입을 소개하고 있다. 스프링에서는 일반적으로 [생성자 주입을 권장한다](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-setter-injection). 생성자 주입을 사용하는 경우 애플리케이션의 구성 요소를 불변 객체로 만들 수 있고 언제나 완전한 상태로 초기화되기 때문에 안전하게 사용할 수 있다. 또한, 생성자의 파라미터가 너무 많은 경우 (주입되는 객체가 너무 많은 경우), 이 클래스에 너무 많은 책임이 있다는 것을 알 수 있기 때문에 리팩터링을 해야 함을 빠르게 인지할 수 있다. setter 주입은 기본값을 할당할 수 있는 선택적인 종속성에만 사용하는 것이 좋다.    

## 순환 종속성 문제
생성자 주입을 사용하는 경우, 순환 종속성(Circular dependencies) 문제가 발생할 수 있다. 순환 종속성이란 의존하는 대상이 서로 물려 있어서 종속성을 주입할 수 없는 현상을 말한다.  

```java
@Service
public class DemoServiceA {
	private final DemoServiceB demoServiceb;

	public DemoServiceA(DemoServiceB demoServiceB) {
		this.demoServiceb = demoServiceB;
	}
}
```
```java
@Service
public class DemoServiceB {
	private final DemoServiceA demoServiceA;
	
	public DemoServiceB(DemoServiceA demoServiceA) {
		this.demoServiceA = demoServiceA;
	}
}
```
```java
@SpringBootApplication
public class DemoApplication {
	public static void main(String[] args) {
		SpringApplication.run(DemoApplication.class, args);
	}
}
```
위 코드에서 DemoServiceA는 생성자에서 DemoServiceB의 인스턴스를 필요로 하며, DemoServiceB는 생성자에서 DemoServiceA의 인스턴스를 필요로 한다. 이 프로젝트를 실행하면 다음의 로그가 출력되면서 프로젝트가 종료된다.

```log
...
2022-04-10 19:19:31.961  WARN 16872 --- [           main] ConfigServletWebServerApplicationContext : Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'demoServiceA' defined in file [/Users/jaein/Downloads/demo 2/build/classes/java/main/com/example/demo/service/DemoServiceA.class]: Unsatisfied dependency expressed through constructor parameter 0; nested exception is org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'demoServiceB' defined in file [/Users/jaein/Downloads/demo 2/build/classes/java/main/com/example/demo/service/DemoServiceB.class]: Unsatisfied dependency expressed through constructor parameter 0; nested exception is org.springframework.beans.factory.BeanCurrentlyInCreationException: Error creating bean with name 'demoServiceA': Requested bean is currently in creation: Is there an unresolvable circular reference?
...
2022-04-10 19:19:31.971  INFO 16872 --- [           main] o.apache.catalina.core.StandardService   : Stopping service [Tomcat]
2022-04-10 19:19:31.989  INFO 16872 --- [           main] ConditionEvaluationReportLoggingListener : 

Error starting ApplicationContext. To display the conditions report re-run your application with 'debug' enabled.
2022-04-10 19:19:32.004 ERROR 16872 --- [           main] o.s.b.d.LoggingFailureAnalysisReporter   : 

***************************
APPLICATION FAILED TO START
***************************

Description:

The dependencies of some of the beans in the application context form a cycle:

┌─────┐
|  demoServiceA defined in file [/Users/jaein/Downloads/demo 2/build/classes/java/main/com/example/demo/service/DemoServiceA.class]
↑     ↓
|  demoServiceB defined in file [/Users/jaein/Downloads/demo 2/build/classes/java/main/com/example/demo/service/DemoServiceB.class]
└─────┘


Action:

Relying upon circular references is discouraged and they are prohibited by default. Update your application to remove the dependency cycle between beans. As a last resort, it may be possible to break the cycle automatically by setting spring.main.allow-circular-references to true.


Process finished with exit code 1
```
스프링은 애플리케이션 컨텍스트를 로드할때 모든 싱글톤 빈을 생성한다. 이 때 순환 종속성 관계에 있는 빈을 만나면 UnsatisfiedDependencyException을 발생시킨다. 권장하지는 않지만 생성자 주입이 아닌 setter 주입으로 구성하면 예외를 피할 수 있다. 혹은, 한 쪽의 생성자 파라미터에 @Lazy 어노테이션을 붙이는 방법도 있다. 
```java
@Component
public class DemoServiceA {
	private final DemoServiceB demoServiceb;

	public DemoServiceA(@Lazy DemoServiceB demoServiceB) {
		this.demoServiceb = demoServiceB;
	}
}
```
일반적으로 객체 지향 설계에서 이런 순환 의존 관계는 안티 패턴이다. 예를 들어, 두 클래스가 상호 의존하는 경우 클래스 간의 결합도가 높아져서 한 클래스가 변경되었을 때 다른 클래스도 같이 변경되어야 할 가능성이 크다. 따라서 A->B, B->A 중 하나를 없애거나, 인터페이스를 활용하여 A->C<-B 구조를 만들거나, 생성 패턴을 활용하여 C->A C->B->A 구조를 만들어서 순환 참조를 끊는 것을 권장한다.

## 참고
https://martinfowler.com/bliki/InversionOfControl.html  
https://dzone.com/articles/ioc-vs-di  
https://www.theserverside.com/feature/Meaning-of-inversion-of-control-in-Spring-and-Java-IoC-explained  
https://www.techtarget.com/searchapparchitecture/definition/dependency-injection  
https://stackoverflow.com/questions/11665168/should-everything-be-spring-managed-in-a-spring-application  
https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-dependencies  