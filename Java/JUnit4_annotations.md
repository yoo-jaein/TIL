# JUnit4 annotation
자바 테스트 프레임워크 JUnit4의 어노테이션

## 예시 코드
```java
public class SampleTest {
    
    @Before
    public void setup() {...}
    
    @BeforeClass
    public void setup_class() {...}
    
    @After
    public void teardown() {...}
    
    @AfterClass
    public void teardown_class() {...}
    
    @Test
    public void test_method() {...}
    
    @Test(timeout = 500)
    public void test_method_timeout() {...}
    
    @Test(expected = IllegalArgumentException.class)
    public void test_method_expected_exception() {...}
    
    @Ignore
    public void ignore_this_method() {...}
}
```

## @Before
@Before를 사용하면 테스트 메서드를 실행하기 전 준비 작업을 할 수 있다. @Before가 붙은 메서드는 테스트 클래스의 **테스트마다 각각 한 번씩 실행된다.**

## @BeforeClass
@BeforeClass를 사용하면 테스트 클래스를 실행하기 전 준비 작업을 할 수 있다. @BeforeClass가 붙은 메서드는 **테스트 클래스에서 단 한 번 실행된다.**

## @After
@After를 사용하면 테스트 메서드를 실행한 후 정리 작업을 할 수 있다. @After가 붙은 메서드는 테스트 클래스의 **테스트마다 각각 한 번씩 실행된다.**

## @AfterClass
@AfterClass를 사용하면 테스트 클래스를 실행한 후 정리 작업을 할 수 있다. @AfterClass가 붙은 메서드는 **테스트 클래스에서 단 한 번 실행된다.**

## @Test
@Test가 붙은 메서드는 JUnit의 테스트 대상이 된다. @Test에는 여러 옵션이 존재한다. timeout을 통해 시간 제한을 걸거나, expected를 통해 발생해야 하는 예외의 종류를 명시할 수 있다.

## @Ignore
@Ignore가 붙은 메서드는 실행 대상에서 제외된다. 예를 들어, 아직 구현이 끝나지 않는 경우 @Ignore로 실행 대상에서 제외시킬 수 있다.

## 참고
https://javarevisited.blogspot.com/2012/06/junit4-annotations-test-examples-and.html  