# Serialization and Deserialization

## 직렬화
자바 직렬화(Serialization)란 객체를 바이트 스트림으로 변환하는 매커니즘이다. 직렬화를 통해 생성된 바이트 스트림은 플랫폼에 독립적이다. 따라서 한 플랫폼에서 직렬화된 객체는 다른 플랫폼에서 역직렬화될 수 있다. 주로 객체를 파일로 저장하거나 다른 서버로 전송할 때 직렬화한다.

자바 객체를 직렬화하고 싶다면 java.io.Serializable 인터페이스를 구현해야 한다. ObjectOutputStream 클래스에는 Object의 직렬화를 위한 writeObject() 메서드가 포함되어 있다.

## Serializable 구현하기
```java
@Setter
@Getter
public class Student implements Serializable {
	private String name;
	private int grade;
	private boolean graduated;
}
```
Serializable는 필드와 메서드가 없는 마커 인터페이스다. 마커 인터페이스는 그 인터페이스를 구현하는 클래스의 객체가 특정 기능을 가질 수 있도록 표시하는 역할을 한다. 만약 Student가 Serializable를 구현하지 않았는데 직렬화한다면 NotSerializableException이 발생한다.

## 역직렬화
역직렬화(Deserialization)은 직렬화된 객체의 바이트 스트림을 이용해 원래의 자바 객체로 다시 변환하는 작업을 말한다. 

## 참고
https://www.geeksforgeeks.org/serialization-in-java/  
https://mkyong.com/java/java-serialization-examples/  
