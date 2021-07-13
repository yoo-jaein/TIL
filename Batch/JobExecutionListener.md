# JobExecutionListener
모든 잡은 생명주기를 갖는다. 이를 바탕으로 스프링 배치는 생명주기의 여러 시점에 로직을 추가할 수 있는 기능을 제공한다. 잡 실행과 관련이 있다면 JobExecutionListener를 사용할 수 있다. 이 인터페이스는 beforeJob, afterJob 두 메서드를 제공한다.  

## 사용 예시
- 알림 : 잡의 시작과 종료를 다른 시스템에 알리는 메시지 큐 메시지를 생성
- 초기화 : 잡 실행 전에 준비
- 정리 cleanup : 실행 이후 정리 작업 수행

## 잡 리스너 작성하기
### JobExecutionListener 인터페이스 구현
첫 번째는 JobExecutionListener 인터페이스를 구현하는 방법이다. 이 인터페이스는 beforeJob, afterJob 메서드를 가진다. afterJob에서 중요한 점은 이 메서드는 잡의 완료 상태에 관계없이 호출된다는 것이다. 이 방법으로 만든 리스너를 사용하려면 JobBuilder의 listener 메서드를 호출하면 된다.

### 어노테이션 이용
두 번째는 @BeforeJob과 @AfterJob 어노테이션을 사용하는 방법이다. 스프링이 특정 목적으로 구현할 인터페이스를 제공한다면, 보통 구현 작업을 더 편하게 만들어주는 어노테이션이 존재한다. 어노테이션을 사용하면 인터페이스를 직접 구현할 필요가 없다. 이 방법으로 만든 리스너를 사용하려면 먼저 래핑을 해야 한 후 설정한다.

## 참고
스프링 배치 완벽 가이드 2/e  