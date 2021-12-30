# Spring AOP

## 프록시 패턴
프록시는 타겟을 감싸서 타겟의 요청을 대신 받아주는 랩핑 오브젝트이다. 프록시는 호출을 가로챈다. 클라이언트에서 타겟을 호출하게 되면 타겟이 아닌 타겟을 감싸고 있는 프록시가 호출되어 타겟 메소드 실행전에 선처리, 타겟 메소드 실행 후, 후처리를 실행시키도록 구성된다.  

프록시 패턴을 사용하면 어떤 기능을 추가하려 할때 기존 코드를 변경하지 않고 기능을 추가할수 있다. 실제 작업을 행하는 오브젝트를 감싸서, 실제 오브젝트를 요청하기 전이나 후에 인가 처리(보호)나, 생성 자원이 많이 드는 작업 백그라운드 처리 (가상), 원격 메소드를 호출하기 위한 작업(원격 프록시) 등을 하는데 사용한다.

## 정적 프록시와 동적 프록시
정적 프록시와 동적 프록시의 차이점은 개발자가 프록시 클래스를 정의해야 하는지 여부이다.

- 정적 프록시 패턴에서는 명시적으로 프록시 클래스를 만들고 직접 논리를 구현한다. 프록시를 직접 구현해야 하기 때문에 프록시 패턴을 적용하려는 타겟이 많은 경우 번거롭다. 예를 들어, 1000개의 클래스가 있을 때 각 클래스에 대해 동일한 로깅 작업을 수행해야 한다면, 1000개의 프록시 클래스를 작성해야 한다. 이는 매우 지루하고 낭비되는 작업일 것이다.

- 동적 프록시 패턴은 기본적으로 프록시 객체가 런타임 중에 동적으로 생성되는 것을 말한다. 동적 프록시의 경우 InvocationHandler를 사용하여 동작을 정의한다. 이것은 동적 프록시 클래스가 InvocationHandler를 전략으로 사용하고 호출을 해당 InvocationHandler에 위임한다는 것을 뜻한다.

## Spring AOP와 동적 프록시
Spring AOP는 Proxy를 기반으로 한 Runtime Weaving(위빙, AOP에서 aspect와 다른 외부 객체들을 이어주는 과정) 방식으로 동작한다. 동적 프록시를 적용하기 위해 JDK Dynamic Proxy와 CGlib를 이용한다. JDK Dynamic Proxy는 [Reflection](https://github.com/yoo-jaein/TIL/blob/main/Java/Reflection.md)을 기반으로 이루어지고, CGlib은 상속을 기반으로 이루어진다. 만약 타겟이 적어도 하나 이상의 인터페이스를 구현하고 있다면 JDK Dynamic Proxy가 이용되고, 그렇지 않으면 CGlib를 이용하여 프록시를 생성한다.    

### Spring AOP 프록시와 빈
어떤 클래스가 Spring AOP의 대상이라면 그 기존 클래스의 빈이 만들어질때, Spring AOP가 프록시 클래스를 자동으로 만들고 원본 클래스 대신 해당 프록시를 빈으로 등록한다. 그리고 원본 클래스가 사용되는 지점에서 프록시를 대신 사용한다.

## JDK Dynamic Proxy 기반 프록시
JDK Dynamic Proxy는 JDK에 내장되어 있다. JDK Dynamic Proxy는 인터페이스가 존재하는 메서드만 프록시를 생성할 수 있다. InvocationHandler를 상속받아서 실체를 구현하는데 이 과정에서 Reflection을 이용하기 때문에 성능이 조금 떨어진다.

## CGlib 기반 프록시
CGlib는 오픈소스 라이브러리다. Enhancer를 바탕으로 프록시를 구현하는 방식이다. JDK Dynamic Proxy와 다르게 Reflection을 사용하지 않고 상속을 이용해서 타겟을 오버라이딩한다. 기본적으로 바이트 코드를 조작해서, 바이너리가 만들어지기 때문에 JDK Dynamic Proxy보다 성능이 좋다. 다만, final 객체 혹은 private 접근자로 된 메서드는 오버라이딩을 지원하지 않기 때문에 프록시 구현이 제약적이다.

## 참고
https://medium.com/@shohraafaque/proxies-in-java-static-dynamic-8ccc51d16346  
https://sabarada.tistory.com/97  
