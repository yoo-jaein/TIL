# POJO framework

## POJO
POJO(Plain Old Java Object)는 마틴 파울러가 만들어냈으며 자바의 단순한 오브젝트를 가리키는 용어이다. 특별한 의미는 없고, 2000년에 마틴 파울러가 컨퍼런스 발표를 준비하던 중 단순한 오브젝트에 대해 'EJB'처럼 그럴싸하게 보이도록 붙인 명칭이다. 자바를 사용해야 하는 것 외에는 제한사항이 없다.

```java
public class Book {
	private String title;
	private String author;
	private int price;

	public Book(String title, String author, int price) {
		this.title = title;
		this.author = author;
		this.price = price;
	}
	
	public String getTitle() {
		return title;
	}

	public String getAuthor() {
		return author;
	}

	public int getPrice() {
		return price;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public void setAuthor(String author) {
		this.author = author;
	}

	public void setPrice(int price) {
		this.price = price;
	}
}
```

위의 Book 클래스는 POJO 클래스의 한 예시이다. 일반적으로 POJO 클래스에는 변수와 getter, setter가 포함된다. POJO는 쉽게 이해할 수 있기 때문에 프로젝트 코드의 가독성과 재사용성을 높인다. 

## 제한 사항
POJO는 다음을 수행해서는 안 된다.  

- 클래스 상속 
  - public class GFG extends javax.servlet.http.HttpServlet {...} 은 POJO 클래스가 아니다.
- 인터페이스 구현
  - public class Bar implements javax.ejb.EntityBean {...} 은 POJO 클래스가 아니다.
- 어노테이션 적용
  - @javax.persistence.Entity public class Baz {...} 은 POJO 클래스가 아니다.

## POJO 프로그래밍
POJO에 애플리케이션 핵심 로직과 기능을 담아서 설계하고 개발하는 방법을 POJO 프로그래밍이라고 한다. 그렇다면 POJO 프로그래밍의 장점은 무엇일까?

1. 깔끔한 코드 : 특정 기술과 비즈니스 로직이 결합되면 코드가 지저분해진다. POJO는 특정 기술과 환경에 종속되지 않기 때문에 깔끔한 코드를 만든다.  
2. 자동화된 테스트
3. 객체지향 설계 적용 : 객체지향 프로그래밍, 도메인 모델, 디자인 패턴 등은 POJO가 아니면 적용하기 힘들다.

## 스프링과 POJO
스프링은 POJO를 이용한 엔터프라이즈 애플리케이션 개발을 목적으로 하는 프레임워크다. 스프링을 사용하면 다음과 같이 POJO 프로그래밍의 장점을 그대로 살릴 수 있다.

- 애플리케이션의 핵심 로직을 객체지향적인 POJO를 기반으로 깔끔하게 구현
- 엔터프라이즈 환경의 각종 서비스와 기술을 POJO 방식의 코드에 적용

스프링은 복잡한 엔터프라이즈 기술을 담당하고 비즈니스 로직 부분은 개발자에게 맡겨 POJO로 구현하게 한다. 따라서 스프링을 제대로 다루기 위해서 개발자는 **객체지향 분석과 설계**에 대해 훈련해야 하고 **자바 언어와 JVM, JDK API의 사용법**에 대해 잘 알아야 한다. **디자인 패턴, 구현 패턴, 리팩토링 기술** 또한 필요하다.

## 참고
토비의 스프링 3.1 Vol.1 8장  
https://www.javatpoint.com/pojo-in-java  
https://www.geeksforgeeks.org/pojo-vs-java-beans/  