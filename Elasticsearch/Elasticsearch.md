# Elasticsearch
일래스틱서치

## Elasticsearch
일래스틱서치(Elasticsearch)는 정보 검색 라이브러리 Apache Lucene(루씬) 기반의 검색 엔진이다. 루씬과 동일하게 자바로 개발되었으며 아파치 라이선스 2.0 오픈소스로 출시되어 있다. 데이터 수집 및 로그 파싱 엔진인 Logstash와 분석 및 시각화 플랫폼인 Kibana와 함께 개발되었다. Elasticsearch-Logstash-Kibana는 서로 연동된 솔루션을 목적으로 설계되었으며, 이를 일래스틱스택(Elastic Stack, ELK stack)이라 부른다.

## Elasticsearch의 장점
### 1. 빠른 구축
REST 기반의 API와 HTTP 인터페이스를 제공하고 스키마에 자유로운 JSON 문서를 사용하기 때문에 다양한 사례를 위한 애플리케이션을 쉽고 빠르게 구축할 수 있다.

### 2. 고성능
분산 특성을 통해 대량의 데이터를 병렬 처리하여 쿼리에 가장 적합한 항목을 빠르게 찾을 수 있다.

### 3. 무료 도구 및 플러그인
분석 및 시각화 플랫폼인 Kibana와 통합된다. 또한, Beats나 Logstash와 통합되어 소스 데이터를 쉽게 변환하고 이를 Elasticsearch 클러스터에 로드할 수 있다. 다양한 오픈소스 플러그인이 존재하기 때문에 풍부한 기능을 추가할 수 있다.

### 4. 준실시간 작업
데이터 읽기나 쓰기와 같은 Elasticsearch 작업은 일반적으로 1초 미만이 소요된다. 따라서 Elasticsearch는 애플리케이션 모니터링과 이상 감지에 거의 실시간으로 사용될 수 있다.

### 5. 다양한 언어 지원
Java, Python, PHP, JavaScript, Node.js, Ruby 등의 다양한 언어를 지원한다.

## 참고
https://en.wikipedia.org/wiki/Elasticsearch  
https://aws.amazon.com/opensearch-service/the-elk-stack/what-is-elasticsearch/  