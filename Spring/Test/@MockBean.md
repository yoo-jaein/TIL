# @MockBean
org.springframework.boot.test.mock.mockito  

테스트를 실행할 때, 때때로 ApplicationContext에 특정 컴포넌트를 mock으로 만들어야 하는 경우가 있다. 스프링 부트가 제공하는 @MockBean을 사용하면 ApplicationContext에 Mockito mock을 넣어줄 수 있다.

## Mockito
자바에서 단위 테스트를 하기 위한 mock을 만들어주는 프레임워크. 

## 내부 동작
스프링 컨텍스트(ApplicationContext)에 mock 객체를 넣어 준다. 만약 동일한 타입의 Bean이 존재할 경우 등록된 MockBean으로 교체해준다. @MockBean은 테스트 클래스에 바로 사용되거나, 테스트의 필드에 사용되거나, @Configuration 클래스나 필드에 사용될 수 있다. 또한, mock 빈은 테스트 메서드가 실행된 이후 매 번 자동으로 리셋된다. 

## 예제
```java
import org.junit.jupiter.api.Test;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;

import static org.mockito.BDDMockito.given;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@WebMvcTest(UserVehicleController.class)
class MyControllerTests {

    @Autowired
    private MockMvc mvc;

    @MockBean
    private UserVehicleService userVehicleService;

    @Test
    void testExample() throws Exception {
        given(this.userVehicleService.getVehicleDetails("sboot"))
                .willReturn(new VehicleDetails("Honda", "Civic"));
        this.mvc.perform(get("/sboot/vehicle").accept(MediaType.TEXT_PLAIN))
                .andExpect(status().isOk())
                .andExpect(content().string("Honda Civic"));
    }
}
```

## 주의!
@Mock, @InjectMocks, @SpyBean와 차이점 알아두기

## 참고
https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.testing.spring-boot-applications.mocking-beans  
https://cobbybb.tistory.com/16  