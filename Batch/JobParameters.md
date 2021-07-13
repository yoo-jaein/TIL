# JobParameters
JobInstance는 잡 이름과 파라미터로 식별된다. 또한 동일한 식별 파라미터를 사용하여 동일한 잡을 두 번 이상 실행할 수 없다. 만약 다시 실행한다면 JobInstanceAlreadyCompleteException이 발생한다.

## 잡 파라미터 입력 예시
잡에 파라미터를 전달하는 방법은 사용자가 잡을 어떻게 호출하는지에 따라 달라진다. 스프링 부트의 JobLauncherCommandLineRunner를 기준으로 파라미터 전달 방법을 알아보자.  

### 예시 1
```
java -jar demo.jar name=Michael
```
name이라는 파라미터 하나를 전달한다.  

### 예시 2 : 타입 설정 
```
java -jar demo.jar executionDate(date)=2020/12/27
```
파라미터 이름 뒤에 괄호를 쓰고 그 안에 파라미터의 타입을 명시해 스프링 배치에 알려줄 수도 있다. 이 때 해당 타입의 이름은 모두 소문자여야 한다.  

### 예시 3 : 인텔리제이 환경
인텔리제이 Run/Debug Configuration (Edit Configurations) - Build and run - Program arguments에 파라미터 기재
```
--job.name=fileToDatabaseJob version=1
```

### 예시 4 : 식별에서 제외
만약 특정 잡 파라미터가 식별에 사용되지 않게 하려면 접두사 -을 사용한다.  
```
java -jar demo.jar executionDate(date)=2020/12/27 -name=Michael
```
여기서 name 파라미터는 식별에 사용되지 않는다. 만약 -name=John으로 변경하더라도 스프링 배치는 기존의 JobInstance를 기반으로 JobExecution을 생성한다.

## 잡 파라미터 확인하기
잡에 전달한 파라미터를 확인하고 싶다면 JobRepository를 살펴보면 된다. JobRepository의 데이터베이스 스키마에는 BATCH_JOB_EXECUTION_PARAMS 테이블이 있다. KEY_NAME, TYPE_CD, VAL 값 등을 보면 된다.  


## 잡 파라미터에 접근하기
- ChunkContext : execute 메서드는 두 개의 파라미터를 전달받는다. 첫 번째는 StepContribution으로 아직 커밋되지 않은 현재 트랜잭션에 대한 정보(쓰기 수, 읽기 수 등)를 가진다. 두 번째 파라미터는 ChunkContext 인스턴스다. 이 인스턴스는 실행 시점의 잡 상태를 제공한다. 또한 태스크릿 내에서는 처리 중인 청크와 관련된 정보, 스텝, 잡과 관련된 정보가 있다. 여기에 JobParamters가 포함되어 있다.

- Late Binding : 늦은 바인딩, 스프링 구성을 사용해 주입하는 방식이다.

스프링 배치는 JobParameter 클래스의 인스턴스에 잡 파라미터를 저장한다. getJobParameters()를 호출하여 잡 파라미터를 가져오면 Map<String, Object>가 반환된다. 그래서 타입 캐스팅이 필요하다.

늦은 바인딩으로 주입하려면 SpEL을 사용해야 한다. 여기서 늦은 바인딩으로 구성될 빈은 스텝 스코프나 잡 스코프를 가져야 한다.
스코프는 스텝의 실행 범위나 잡의 실행 범위에 들어갈 때까지 빈 생성을 지연시킨다. 이렇게 하면 명령행 또는 다른 소스에서 받아들인 잡 파라미터를 빈 생성 시점에 주입할 수 있다.

## 참고
스프링 배치 완벽 가이드 2/e  