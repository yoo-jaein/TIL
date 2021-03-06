# Spring Batch
스프링 배치 프레임워크는 일반적인 배치 패턴 및 패러다임을 구현하는 표준 기반 방법이다.  
스프링 배치에는 데이터 유효성 검증, 출력 포매팅, 복잡한 비즈니스 규칙을 재사용 가능한 방식으로 구현하는 기능, 대규모 데이터셋 처리 기능이 구현돼 있다.  

## 구조
스프링 배치는 레이어 구조로 조립된 세 개의 티어로 구성된다.  

먼저, 가장 바깥쪽에 애플리케이션 레이어가 있다. 애플리케이션 레이어는 배치 처리 구축에 사용되는 모든 사용자 코드와 구성이 포함된다. 비즈니스 로직, 서비스는 물론 잡 구조화와 관련된 구성까지 포함된다. 이 레이어는 다른 두 레이어인 코어 레이어와 인프라스트럭쳐 레이어를 감싸고 있다.  

코어 레이어는 애플리케이션 레이어와 상호작용을 하는 레이어이다. 코어 레이어에는 배치 도메인을 정의하는 모든 부분이 포함된다. 잡, 스템 인터페이스와 잡 실행에 사용되는 잡런처, 잡파라미터 인터페이스 등이 해당된다.  

이 모든 것의 밑에는 인프라스트럭처 레이어가 있다. 어떤 처리를 수행하려면 파일, 데이터베이스 등으로부터 읽고 쓸 수 있어야 한다. ItemReader, ItemWriter를 비롯해 재시작과 관련된 클래스와 인터페이스가 포함된다.  

## 참고
스프링 배치 완벽 가이드 2/e  