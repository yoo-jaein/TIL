# Job

## 정의
처음부터 끝까지 독립적으로 실행할 수 있으며, 고유하고 순서가 지정된 여러 스텝의 목록  
- 유일하다 : 스프링 배치의 잡은 스프링 빈 구성 방식과 동일하게 자바나 XML을 사용해서 구성하고 재사용할 수 있다.
- 순서를 가진 여러 스텝의 목록이다 : 모든 스텝을 논리적인 순서대로 실행할 수 있도록 잡을 구성한다.
- 처음부터 끝까지 실행 가능하다 : 배치 처리는 어떠한 완료 상태에 도달할 때 까지 추가적인 상호작용 없이 실행하는 처리이다. 외부 의존성 없이 실행해야 한다.

## 잡의 실행
잡의 실행은 잡 러너 job runner에서 시작된다. 잡 러너는 잡 이름과 여러 가지 파라미터를 받아서 잡을 실행시킨다. 스프링 배치는 두 가지 잡 러너를 제공한다.  
- CommandLineJobRunner : 스크립트를 이용하거나 명령행에서 직접 잡을 실행할 때 사용. 스프링을 부트스트랩하고 전달받은 파라미터를 사용해 요청된 잡을 실행.
- JobRegistryBackgroundJobRunner : JobRegistry를 생성하는데 사용. 스프링을 부트스트랩해서 기동한 자바 프로세스 내에서 스케줄러를 사용해서 잡을 실행한다면, 스프링이 부트스트랩될 때 실행 가능한 잡을 가지고 있는 잡 레지스트리를 생성한다.
- JobLauncherCommandLineRunner : CommandLineRunner 구현체. 애플리케이션 컨텍스트에 정의된 잡 타입의 모든 빈을 기동 시에 실행한다. 스프링 부트가 제공.

프레임워크를 실행할 때 실제 진입점은 잡 러너가 아닌 JobLauncher 인터페이스의 구현체다. 스프링 배치는 SimpleJobLauncher라는 단일 JobLauncher를 제공한다. CommandLineJobRunner와 JobLauncherCommandLineRunner 내에서 사용하는 이 클래스는 요청된 잡을 실행할 때 코어 스프링의 TaskExecutor 인터페이스를 사용한다.  

## JobInstance
잡 인스턴스는 잡의 논리적 실행을 의미한다. 잡 이름과 파라미터로 식별된다. 잡 인스턴스는 한 번 성공적으로 완료되면 다시 실행시킬 수 없다. 동일한 식별 파라미터를 사용하는 잡은 한 번만 실행할 수 있다.

## JobExecution
JobExecution은 잡 실행의 실제 시도를 의미한다. 잡이 처음부터 끝까지 단번에 실행 완료됐다면 해당 JobInstance와 JobExecution은 하나씩만 존재한다. 첫 번째 실행 후 오류 상태로 종료됐다면, 해당 JobInstance를 실행하려고 시도할 때마다 JobExecution이 새롭게 생성된다.

## @EnableBatchProcessing
애플리케이션 내에서 한 번만 적용하면 되며, 배치 잡 수행에 필요한 인프라를 제공한다.

## JobBuilderFactory, StepBuilderFactory
각각 JobBuilder, StepBuilder 인스턴스를 생성하며, 이는 스프링 배치 잡과 스텝을 생성하는데 사용된다. JobBuilderFactory.get 메서드를 호출하면서 잡 이름을 전달하면 JobBuilder를 얻을 수 있다. 이 빌더를 사용해 잡을 구성한다.

## 참고
스프링 배치 완벽 가이드 2/e  