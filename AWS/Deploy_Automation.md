# Deploy Automation
클라우드의 장점 중 하나가 API로 모든 것을 컨트롤 할 수 있다는 것이다.  
이 장점을 통해 기능을 자동화할 수 있다.  

## 자동화 방법
쉘 스크립트  
CloudFormation  
OpsWorks or 테라폼 등

## CloudFormation
json 또는 yaml 스크립트로 구축한다.
기본으로 사용하기에는 귀찮은 점이 있다. 그래서 CloudFormation을 래핑한 서비스들을 많이 사용한다.  
AWS 리소스의 집합을 단일 단위(스택)으로 생성, 업데이트, 삭제할 수 있다.  

## Elastic Beanstalk
EC2 기반 애플리케이션을 배포할 때 사용한다.  
내부적으로 CloudFormation을 사용한다.

## CloudFormation 구성
템플릿(json/yaml), 스택(AWS 리소스 모음. S3, EC2, Glacier 등)으로 이루어진다.  
템플릿은 형상관리를 해주고 코드 리뷰도 해주는게 좋다.  

## 주의!
스택으로 생성된 리소스들은 스택이 삭제되면 다 함께 삭제된다.  
