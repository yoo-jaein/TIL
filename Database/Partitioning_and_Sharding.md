# Partitioning and Sharding
파티셔닝과 샤딩

## Partitioning
단일 시스템에서 데이터베이스를 실행할 때 컴퓨팅 리소스 양의 제한이 있기 때문에 효율적으로 작업할 수 있는 최대 데이터 양에 도달하게 된다. 이는 하나의 DBMS가 많은 테이블을 관리하다 보니 생기는 문제다. 이를 해결하기 위한 방법이 파티셔닝(Partitioning)이다. 파티셔닝 기법을 쓰면 데이터베이스 서버에서 데이터를 별도의 테이블로 분리해서 저장하지만 사용자 입장에서는 여전히 하나의 테이블로 읽기, 쓰기를 할 수 있게 해준다. 일반적으로 DBMS의 파티셔닝은 하나의 서버에서 테이블을 분산하는 것이다.  

### 장점
- 파티션단위로 I/O 부하가 분산된다. 주로 대용량 Data Write 환경에서 효율적이다.
- 물리적인 파티셔닝으로 전체 데이터의 훼손 가능성이 줄어든다.
- 파티션별로 백업, 복구할 수 있기 때문에 관리가 용이해진다.

### 단점
- 테이블 간 Join 비용이 증가한다.
- 테이블과 인덱스를 별도로 파티셔닝할 수 없기 때문에 항상 같이 파티셔닝해야 한다.

## 파티셔닝 기준
관계형 데이터베이스 관리 시스템(RDBMS)은 데이터베이스를 파티셔닝하는 다양한 기준을 제공한다. 파티션 키(Partition key)를 사용하여 데이터를 파티셔닝하는데, 이는 데이터의 성향에 따라 결정해야 한다. MySQL은 범위 파티셔닝, 리스트 파티셔닝, 해시 파티셔닝, 키 파티셔닝, 서브 파티셔닝 기능까지 제공한다. 

### 범위 파티셔닝(Range partitioning)
```mysql
CREATE TABLE logs (
    log_idx INT NOT NULL,
    reg_date DATETIME NOT NULL,
    PRIMARY KEY(log_idx, reg_date)
)
PARTITION BY RANGE (YEAR (reg_date)) (
    PARTITION log_2018 VALUES LESS THAN (2019),
    PARTITION log_2019 VALUES LESS THAN (2020),
    PARTITION log_2020 VALUES LESS THAN (2021),
    PARTITION log_etc VALUES LESS THAN MAXVALUE
);
```
- 연속적인 숫자나 날짜 기준으로 파티셔닝
- 우편번호, 일별, 월별, 분기별 등의 데이터에 적합하다.

### 리스트 파티셔닝(List partitioning)
```mysql
CREATE TABLE books (
    book_id INT NOT NULL,
    category_id INT NOT NULL,
    PRIMARY KEY(book_id, category_id)
)
PARTITION BY LIST (category_id) (
    PARTITION sf VALUES IN (1, 2, 3),
    PARTITION thriller VALUES IN (4, 5, 6),
    PARTITION romance VALUES IN (7, 8, 9),
    PARTITION etc VALUES IN (10, NULL)
);
```
- 값 목록에 파티션을 할당 
- 파티션 키 값을 그 목록에 비추어 파티션을 선택
- 분포도가 비슷하며, 많은 SQL에서 해당 Column의 조건이 많이 들어오는 경우 유용하다.

### 해시 파티셔닝(Hash partitioning)
```mysql
CREATE TABLE member (
    member_id INT NOT NULL,
    name VARCHAR(10) NOT NULL,
    PRIMARY KEY(member_id)
)
PARTITION BY HASH (member_id) 
    PARTITIONS 4 (
    PARTITION member_1,
    PARTITION member_2,
    PARTITION member_3,
    PARTITION member_4
);
```
```mysql
CREATE TABLE student (
    student_id INT NOT NULL, 
    class VARCHAR(8),
    date_of_admission DATE NOT NULL DEFAULT '2000-01-01'
) 
PARTITION BY HASH(YEAR(date_of_admission)) 
PARTITIONS 4;
```
- 파티션 키의 Hash 값을 기준으로 파티셔닝 
- 데이터를 균등하게 분할한다.
- 특정 데이터가 어느 파티션에 있는지 판단할 수 없다.
- MySQL의 해시 함수는 파티션 표현식의 결과 값을 파티션 개수로 나눈 나머지(MOD(partition_key, num_of_partition))로 저장될 파티션을 결정하는 방식이다.
- 예를 들어, 4개의 파티션으로 분할하는 경우 해시 함수는 0~3의 정수를 돌려준다.
- 해시 파티셔닝은 파티션의 개수가 바뀌면 MySQL의 해시 함수에 의해 파티션에 들어가있는 레코드의 재배치가 이루어지기 때문에 주의해서 사용해야 한다.

### 키 파티셔닝(Key partitioning)
```mysql
CREATE TABLE member2 (
    member_id INT NOT NULL,
    name VARCHAR(10) NOT NULL,
    PRIMARY KEY(member_id)
)
PARTITION BY KEY () 
    PARTITIONS 4 (
    PARTITION member_1,
    PARTITION member_2,
    PARTITION member_3,
    PARTITION member_4
);
```
- 해시 파티셔닝의 특수한 형태다. 
- 해시 파티셔닝은 해시 함수로 사용자 정의 표현식을 사용하지만, 키 파티셔닝은 MySQL 서버에 내장된 해시 함수를 사용한다.

### 합성 파티셔닝(Composite partitioning)
- 파티션에 대해 다시 파티셔닝하는 것(서브 파티셔닝)이다.
- 큰 파티션에 대한 I/O 요청을 여러 파티션으로 분산할 수 있다.
- Range partitioning-List partitioning, Range partitioning-Hash partitioning

## 파티셔닝 방법
### 수평 파티셔닝(Horizontal partitioning)
- 테이블의 행을 기준으로 파티셔닝한다.
- 한 테이블이 담고 있는 데이터의 개수가 작아지고 인덱스의 개수도 작아지기 때문에 성능이 향상된다.
- 풀 스캔 시, 데이터를 찾는 과정이 기존보다 복잡하기 때문에 latency가 증가하게 된다.

### 수직 파티셔닝(Vertical partitioning)
- 테이블의 컬럼을 기준으로 파티셔닝한다.
- 이미 정규화가 완료된 데이터를 분리하는 과정이다.
- 자주 사용되는 컬럼들을 분리시켜 성능을 향상시킬 수 있다.

## Horizontal partitioning vs Sharding
이 둘의 차이점은 샤딩은 데이터가 여러 컴퓨터에 분산되어 있음을 의미하지만 파티셔닝은 그렇지 않다는 것이다. 파티셔닝은 일반적으로 데이터베이스 서버의 단일 인스턴스 내에서 분할하는 것이고, 여러 인스턴스로 분할하는 것이다. 샤딩은 스키마를 복제한 다음 샤드 키를 기반으로 데이터를 나눈다. 따라서 일단 샤딩 되면, 각 샤드는 완전히 별도의 논리적 스키마 인스턴스, 물리적 데이터베이스 서버, 데이터 센터, 리전에 존재할 수 있다. 

### 샤딩 예제
- 인덱스 크기를 줄이고 싶을 때. 테이블이 여러 서버에 분산되므로 각 데이터베이스의 각 테이블에 있는 총 행 수가 줄어든다. 파티셔닝과 마찬가지로 인덱스 크기가 줄어들어 검색 성능이 향상된다.
- 데이터베이스 샤드가 데이터의 실제 분할을 기반으로 할 때. 예를 들어, 유럽 고객의 데이터와 미국 고객의 데이터는 분리되어도 각각의 서비스에 지장이 없다면 지리별로 데이터를 분할해도 좋다.

### 샤딩의 제약 사항
샤딩의 대표적인 제약 사항은 아래와 같다.

- 데이터베이스 인스턴스가 증가하므로 운영 포인트가 증가한다.
- 샤딩 논리를 처리하기 위해 복잡한 SQL을 작성해야 한다.
- 스키마 수정, 열 추가/삭제 등이 훨씬 어려워지므로 데이터베이스 운영이 복잡해진다.

## 참고
https://en.wikipedia.org/wiki/Partition_(database)  
https://en.wikipedia.org/wiki/Shard_(database_architecture)  
https://nesoy.github.io/articles/2018-02/Database-Partitioning  
Real MySQL 8.0  
https://dev.mysql.com/doc/refman/8.0/en/partitioning-hash.html  