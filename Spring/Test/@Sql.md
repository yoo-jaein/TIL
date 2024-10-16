# @Sql
@Sql은 통합 테스트 중에 지정된 데이터베이스에 대해 실행할 SQL script() 및 statement()를 구성하기 위한 어노테이션이다. 

## 적용하기 
테스트 클래스 또는 테스트 메서드에서 사용된다. 테스트 클래스와 테스트 메서드 모두에 @Sql을 적용하는 경우, 기본적으로 메서드 레벨의 @Sql 선언이 클래스 레벨의 선언을 재정의한다. 그러나 Spring Framework 5.2부터 @SqlMergeMode를 통해 오버라이드할지 머지할지를 선택할 수 있게 되었다. 

## @SqlMergeMode
```java
@SpringJUnitConfig(TestConfig.class)
@Sql("/test-schema.sql")
@SqlMergeMode(MERGE) 
class UserTests {

    @Test
    @Sql("/user-test-data-001.sql")
    void standardUserProfile() {
        
    }
}
```

```java
@SpringJUnitConfig(TestConfig.class)
@Sql("/test-schema.sql")
class UserTests {

    @Test
    @Sql("/user-test-data-001.sql")
    @SqlMergeMode(MERGE) 
    void standardUserProfile() {
        
    }
}
```

메서드 레벨의 @Sql 선언과 클래스 레벨의 @Sql 선언을 병합하기를 활성화하려면 @SqlMergeMode(MERGE)를 사용한다. 만약 기본 설정을 따르고 싶다면 @SqlMergeMode를 지우거나 @SqlMergeMode(OVERRIDE)를 통해 전환할 수 있다.  

## 기본 스크립트 찾아보기
만약 SQL 스크립트나 문장이 선언되어 있지 않다면, @Sql이 선언된 위치에 따라 기본 스크립트가 검색된다.  

- 클래스 레벨에 선언되어 있는 경우 : 테스트 클래스가 ```com.example.MyTest```라면, 해당되는 기본 스크립트는 ```classpath:com/example/MyTest.sql```이다.
- 메서드 레벨에 선언되어 있는 경우 : 테스트 메서드가 ```testMethod()```이고 ```com.example.MyTest``` 클래스에 정의되어 있다면, 해당되는 기본 스크립트는 ```classpath:com/example/MyTest.testMethod.sql```이다.

## 여러 개의 스크립트 선언하기
테스트 클래스나 테스트 메서드에서 여러 개의 SQL 스크립트를 필요로 하고 설정을 각각 다르게 만들어야 하는 경우, @Sql 어노테이션을 여러 번 사용하여 개별적으로 선언해줄 수 있다. 

```java
@Test
@Sql(scripts = "/test-schema.sql", config = @SqlConfig(commentPrefix = "`"))
@Sql("/test-user-data.sql")
void userTest() {

}
```

위의 코드에서는 각 @Sql 라인마다 다른 설정이 적용된다.

```java
@Test
@SqlGroup({
    @Sql(scripts = "/test-schema.sql", config = @SqlConfig(commentPrefix = "`"))
    @Sql("/test-user-data.sql")
})
void userTest() {
    
}
```

위의 코드도 동일하게 각 @Sql 라인마다 다른 설정이 적용된다. 자바 8 이상에서 @SqlGroup을 사용하는 것은 선택적이지만, Kotlin과 같이 다른 JVM 언어를 함께 사용하는 프로젝트의 경우 호환성을 위해 @SqlGroup을 사용해야 할 수도 있다.  

## 참고
https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/context/jdbc/Sql.html  
https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#spring-testing-annotation-sql  
https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#testcontext-executing-sql-declaratively  