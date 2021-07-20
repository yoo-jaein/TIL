# Spring Batch Test BeanCreationException

## 프로젝트 환경
- Spring Boot 2.3.x
- H2 데이터베이스
- JUnit4 
- 테스트 클래스에 @SpringBatchTest, @SpringBootTest 적용

## 예외 메시지와 상황
```
BeanCreationException: 'scopedTarget.jobParameter': injection of autowired dependencies failed; ...
```
테스트 클래스에 JobConfiguration 파일을 임포트했다. 이 배치 Job은 날짜 JobParameters를 동적으로 주입받는다. 테스트 클래스에서 JobLauncherTestUtils로 배치 Job을 직접 런칭시켰다. 테스트가 올라가면서 Job 실행에서 BeanCreationException이 발생했다.  

## 해결 방법
테스트 메서드의 내용을 모두 주석처리 한 상태로 클래스를 실행해봤을 때도 동일한 예외가 발생했다. 테스트 코드가 올라갈 때 JobConfiguration 클래스의 잡이 무조건 실행되고 있었다. 관련 내용을 중심으로 검색해보니 다음과 같은 해결 방법이 있었다.

```yml
spring:
  batch:
    job:
      enabled: false
```

이 설정의 기본 값은 true이고, false로 설정하지 않으면 자동으로 배치 Job이 실행된다. 내 프로젝트 코드의 경우 JobLauncherTestUtils로 직접 런칭시켰기 때문에 배치 Job이 두 번 실행되었고, 첫 번째 실행에서 JobParamters를 찾지 못해 예외가 발생했던 것이다. 설정을 변경한 이후 테스트 코드가 정상적으로 작동하는 것을 확인했다.

## 참고
https://kwonnam.pe.kr/wiki/springframework/springboot/batch  