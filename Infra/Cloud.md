# Cloud
클라우드 컴퓨팅이란 네트워크, 서버, 스토리지, 애플리케이션, 서비스 등 구성 가능한 컴퓨팅 리소스의 공유 풀에 대해 편리하고 언제든지 액세스할 수 있으며, 최소의 관리 노력 또는 서비스 제공업체 간의 상호 작동에 의해 신속하게 제공되어 이용할 수 있는 모델 중 하나이다.  

## 서비스 모델
Service Model  
클라우드를 서비스의 차이에 따라 분류하는 개념

![image](https://postfiles.pstatic.net/MjAyMTA2MDdfMjI5/MDAxNjIzMDQ3MDQ4OTgy.E6YDiKRWzHngBzZxXS8Hz7o4aRWKy7rd9hbzGkffHFkg.-DgG3PKAuizTT9s1vuVEro96qy5LqL6zND0Tw5IqrEwg.PNG.y_jaein/image.png?type=w966)  

### SaaS
Software as a Service  
이메일이나 문서 작성, 재무 회계나 판매 관리 등 애플리케이션을 제공하는 서비스이다.  
사용자는 애플리케이션을 실행하기 위한 하드웨어나 OS에 대한 지식이 없어도 기능만 이해하고 있다면 사용할 수 있다.  
Salesforce.com, Google G Suite, Microsoft Office 365 등

### PaaS
Platform as a Service  
애플리케이션 개발이나 실행을 위해 필요한 기능은 제공하는 서비스이다.  
OS, 데이터베이스, 개발 툴, 라이브러리 등을 제공한다.  
Microsoft Azure, Force.com, Google App Engine 등

### IaaS
Infrastructure as a Service  
서버나 스토리지와 같은 하드웨어의 기능을 제공하는 서비스이다.  
필요에 따라 설정만으로 스토리지 용량, 서버의 수, 라우터와 방화벽같은 네트워크 기능을 구축할 수 있다.  
Amazon EC2 등

## 배치 모델
Deployment Model  
클라우드를 시스템의 설치 장소에 따라 분류하는 개념

![image](https://postfiles.pstatic.net/MjAyMTA2MDdfMjc2/MDAxNjIzMDQ3MDY5ODc2.4F-Scng4VQLbRe-LCyYJJ3tupIFrer9oGQu2Enr8GYQg.Knn-Q2SsRejJ_frxcIIc2KT-riPDCCNxotlEptGLkZcg.PNG.y_jaein/image.png?type=w966)  

### 프라이빗 클라우드
기업이 시스템 자원을 직접 소유하여 자사 전용 클라우드로 사용하는 형태.

### 퍼블릭 클라우드
여러 사용자 기업이 인터넷을 통해 공유하는 형태.

### 하이브리드 클라우드
퍼블릭과 프라이빗 클라우드를 조합한 형태.
특정 기업이 소유하는 프라이빗 클라우드는 물리적인 규모와 성능에 제한을 받는다. 이를 퍼블릭 클라우드와 조합하여 하나의 시스템처럼 사용할 수 있다면 실질적으로 제한을 신경쓰지 않고 규모와 성능을 늘릴 수 있다.  
이와 같이 퍼블릭과 프라이빗 각각의 장점을 살려 편리성과 퍼포먼스가 높은 시스템을 실현하는 것이 하이브리드 클라우드이다.

### 참고! 멀티 클라우드
다른 퍼블릭 클라우드끼리 조합하여 자신에게 최적의 클라우드 서비스를 실현하는 형태.  
예를 들어 IoT의 데이터 수집과 집약에는 전용 서비스를 제공하는 AWS, 데이터 분석에는 계산 코스트 퍼포먼스가 좋은 Google Cloud Platform, 그 결과를 제공할 때는 화면 설계나 모바일 지원이 편한 Salesforce.com을 이용하는 방법이다.  
퍼블릭 클라우드 사업자들의 좋은 점만 취해서 자신에게 가장 적합한 조합을 실현시킨다는 장점이 있다.  
또한, 한 회사의 서비스에만 의존하면 위험 부담이 크므로 여러 서비스에 시스템을 분산시키고 트러블 내구성을 높이자는 의도도 있다.  
하지만 운용 관리가 어렵고 비용이 늘어날 수 있다는 단점도 있다.

## 클라우드의 특징
1. 온디맨드 및 셀프 서비스

2. 폭넓은 네트워크 액세스

3. 리소스 공유

4. 신속한 확장성

5. 서비스 측정 가능 및 종량제 요금 가능