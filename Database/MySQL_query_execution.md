# MySQL query execution
MySQL 쿼리 실행 과정

## MySQL 쿼리가 실행되는 과정
```text
SQL 요청 -> [MySQL 엔진 : 쿼리 파서 -> 전처리기 -> 옵티마이저 -> 쿼리 실행기] -> [스토리지 엔진] -> [MySQL 엔진 : 쿼리 실행기] -> SQL 결과 응답
```
1. 사용자는 SQL 문장을 MySQL 서버로 보낸다.
2. MySQL 8.0 이전의 서버는 쿼리 캐시를 확인한다. 데이터가 있으면 캐시 데이터를 반환한다. 그렇지 않으면 SQL 문장을 다음 단계로 전달한다.
3. MySQL 서버는 SQL을 파싱하고 전처리하고 최적의 실행 계획을 만든다. 
4. 쿼리 실행 엔진은 스토리지 엔진의 API를 호출하여 그 계획을 실행한다.
5. MySQL 서버는 결과를 사용자에게 보낸다.

### 쿼리 캐시
가장 먼저 MySQL 서버는 쿼리 캐시(Query cache)를 확인한다. 쿼리 캐시에는 SELECT 문장만 저장할 수 있고 그 문장의 결과 집합도 함께 저장된다. 사용자가 요청한 쿼리 문장이 캐시에 있는 것과 동일한 경우 MySQL 서버는 그 데이터를 즉시 전달한다.  

MySQL 8.0부터 쿼리 캐시 기능은 완전히 제거되고, 관련된 시스템 변수도 모두 제거됐다. 쿼리 캐시는 테이블의 데이터가 변경되면 캐시에 저장된 결과 중 변경된 테이블과 관련된 것들은 모두 삭제 처리해야 했다. 이는 동시 처리에서 심각한 성능 저하를 일으켰다. 쿼리 캐시는 쓰기 작업이 거의 없고 읽기 작업만 하는 서비스에서는 훌륭한 역할을 수행했지만, 대부분의 실제 서비스에서는 큰 도움이 되지 않았고 오히려 많은 버그의 원인이 되었기 때문에 삭제되었다.

### 쿼리 파서
이 단계부터 SQL 쿼리는 쿼리 실행 엔진의 실행 계획으로 전환된다. 실행 계획에는 구문 분석(파싱), 전처리, 최적화 같은 여러 하위 단계가 있다. 쿼리 파서(Query parser)는 사용자 요청으로 들어온 쿼리 문장을 MySQL이 이해할 수 있는 최소 단위의 어휘인 토큰으로 분리해 트리 형태의 구조(parse tree)로 만드는 작업을 수행한다. 쿼리 문장의 기본적인 문법 오류는 쿼리 파서에서 발견된다.

### 전처리기
전처리기(Preprocessor)는 파싱 과정에서 만들어진 파스 트리를 기반으로 쿼리 문장에 구조적인 문제점이 있는지 확인한다. 전처리기는 각 토큰에 테이블 이름, 컬럼 이름 등의 객체를 매핑해서 데이터베이스 객체가 존재하고 있는지, 접근 권한은 있는지 확인한다. 실제 존재하지 않거나 권한상 사용할 수 없는 객체의 토큰은 이 단계에서 걸러진다.

### 옵티마이저
파스 트리가 유효하다는 것이 확인되면 옵티마이저(Optimizer)가 그 쿼리를 어떻게 하면 저렴한 비용으로 가장 빠르게 처리할 수 있을지 실행 계획을 세운다. 하나의 쿼리는 하나의 결과를 생성하기 위해 다양한 방법으로 실행될 수 있다. 옵티마이저의 역할은 여러 선택지 중 최상의 옵션을 찾는 것이다. (하지만 통계는 100% 정확한 것이 아니기 때문에 항상 최상의 계획을 세우는 것은 아니다.) MySQL은 비용 기반 옵티마이저를 사용한다. 즉, 다양한 실행 계획의 비용을 예측하고 가장 저렴한 것을 선택한다. 비용 단위는 단일 무작위 4KB 데이터 페이지를 읽어보는 것이다.  

```mysql
mysql> SELECT SQL_NO_CACHE COUNT(*) FROM sakila.film_actor;
+----------+
| count(*) |
+----------+
|     5462 |
+----------+
mysql> SHOW STATUS LIKE 'last_query_cost';
+-----------------+-------------+
| Variable_name   | Value       |
+-----------------+-------------+
| Last_query_cost | 1040.599000 |
+-----------------+-------------+
```

옵티마이저가 추정한 실행 비용이 궁금하다면 쿼리를 실행한 다음 Last_query_cost 세션 변수를 검사하여 비용을 확인해볼 수 있다. 위 결과는 옵티마이저가 첫 번째 쿼리를 실행하기 위해 약 1,040개의 랜덤 데이터 페이지 읽기를 수행해야 한다고 추정했다는 것을 뜻한다. 기본적으로 옵티마이저는 캐싱이 적용되지 않는 상황을 가정하며 모든 읽기가 디스크 I/O 작업을 필요로 한다고 가정한다. 또한, 테이블의 페이지 수, 인덱스의 페이지 수, 인덱스의 카디널리티, 행의 길이, 키의 길이, 키 분포 등과 같은 통계를 기반으로 실행 비용을 추정한다.

### 실행 엔진
실행 엔진(Execution engine)은 옵티마이저가 세운 계획대로 각 스토리지 엔진에 요청하고 받은 결과를 또 다른 스토리지 엔진의 요청으로 보내주는 역할을 한다. 예를 들어 옵티마이저가 GROUP BY를 처리하기 위해 임시 테이블을 사용하기로 결정했다고 해보자. 그러면 실행 엔진은 다음 과정을 수행한다.
1. 스토리지 엔진에게 임시 테이블을 만들라고 요청한다.
2. 스토리지 엔진에게 WHERE 절에 일치하는 레코드를 읽어오라고 요청한다.
3. 스토리지 엔진에게 읽어온 레코드들을 1번에서 준비한 임시 테이블에 저장하라고 요청한다.
4. 스토리지 엔진에게 임시 테이블로부터 필요한 방식으로 데이터를 읽어오라고 요청한다.
5. 받은 결과를 사용자나 다른 모듈로 넘긴다.

### 스토리지 엔진(핸들러)
스토리지 엔진(Storage engine)은 실행 엔진의 요청에 따라 데이터를 디스크로 저장하고 디스크로부터 데이터를 읽어 오는 역할을 한다. 핸들러라고도 부른다. MyISAM 테이블을 조작하는 경우 핸들러가 MyISAM 스토리지 엔진이 되고, InnoDB 테이블을 조작하는 경우에는 핸들러가 InnoDB 스토리지 엔진이 된다.

## 참고
https://www.oreilly.com/library/view/high-performance-mysql/9780596101718/ch04.html  
https://shashwat-creator.medium.com/mysqls-logical-architecture-1-eaaa1f63ec2f  
Real MySQL 8.0 1권 4장  