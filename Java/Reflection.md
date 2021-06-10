# Reflection
구체적인 클래스 타입을 알지 못해도 그 클래스의 정보(메서드, 타입, 변수 등)에 접근할 수 있게 해주는 자바 API  

## 원리
자바에서는 JVM이 실행되면 사용자가 작성한 자바 코드가 컴파일러를 거쳐 바이트 코드로 변환되어 static 영역에 저장된다. Reflection API는 이 정보를 활용한다. 그래서 클래스 이름만 알고 있다면 언제든 static 영역을 뒤져서 정보를 가져올 수 있는 것이다.

## 활용
실제로 코드를 작성할 때 Reflection을 활용할 일은 거의 없다. 또한, 강력한 API인 만큼 치명적인 단점들을 가지고 있다.

## 단점
성능 오버헤드  
컴파일 시점이 아닌 런타임에 동적으로 타입을 분석하고 정보를 가져오기 때문에 JVM을 최적화할 수 없다. 또한, 직접 접근할 수 없는 private 인스턴스 변수, 메서드에 접근하기 때문에 추상화가 깨진다. 이로 인해 예기치 못한 부작용이 발생할 수 있다.  

## 결론
Reflection은 애플리케이션 개발보다는 프레임워크나 라이브러리에서 많이 사용된다. 사용자가 어떤 클래스를 만들지 예측할 수 없기 때문에 이를 동적으로 해결하기 위해 Reflection을 활용한다.  
Spring의 BeanFactory, Spring Data JPA에서 Entity에 기본 생성자가 필요한 이유, IntelliJ의 자동 완성, Jackson 라이브러리, Hibernate 등

## 참고
https://dundung.tistory.com/222  
https://woowacourse.github.io/javable/post/2020-07-16-reflection-api/  