# MySQL InnoDB
MySQL InnoDB 스토리지 엔진의 특징

## 특징
### 1. 프라이머리 키에 의한 클러스터링
InnoDB의 모든 테이블은 기본적으로 프라이머리 키를 기준으로 클러스터링되어 저장된다. 즉, 프라이머리 키 값의 순서대로 디스크에 저장된다. 모든 세컨더리 인덱스는 레코드의 주소 대신 프라이머리 키의 값을 논리적인 주소로 사용한다. 프라이머리 키로 클러스터링되어 있기 때문에 프라이머리 키를 이용한 범위 스캔은 상당히 빨리 처리될 수 있다. 그래서 쿼리의 실행 계획에서 다른 보조 인덱스보다 프라이머리 키가 선택될 확률이 높다. 참고로 InnoDB 스토리지 엔진과는 달리 MyISAM 스토리지 엔진에서는 클러스터링 키를 지원하지 않는다. 

### 2. 외래 키 지원
InnoDB에서 외래 키는 부모 테이블과 자식 테이블 모두에 해당 칼럼의 인덱스를 생성하는 작업이 필요하고, 변경 시에는 반드시 부모 테이블이나 자식 테이블에 데이터가 있는지 체크하는 작업이 필요하므로 잠금이 여러 테이블로 전파되고, 그로 인해 데드락이 발생할 때가 많으므로 개발할 때도 외래 키의 존재에 주의하는 것이 좋다. (외래 키는 데이터베이스 서버 운영의 불편함 때문에 서비스용 데이터베이스에는 생성하지 않는 경우도 자주 있다.)  

긴급한 조치가 필요할 때 foreign_key_checks 시스템 변수를 OFF로 설정하면 외래 키 관계에 대한 체크 작업을 멈출 수 있다. foreign_key_checks가 비활성화되면 ON DELETE CASCADE, ON UPDATE CASCADE 옵션도 무시된다. 만약 외래 키 체크를 일시적으로 중지한 상태에서 외래 키 관계를 가진 부모 테이블의 레코드를 삭제했다면 반드시 자식 테이블의 레코드도 삭제해서 일관성을 맞춰준 후에 외래 키 체크 기능을 활성화해야 한다. 

### 3. Multi Version Concurrency Control
MVCC의 가장 큰 목적은 잠금을 사용하지 않는 일관된 읽기를 제공하는 데 있다. InnoDB는 언두 로그(Undo log)를 이용해서 MVCC를 구현한다. 멀티 버전이라는 것은 하나의 레코드에 대해 여러 개의 버전이 동시에 관리된다는 의미다.  

예를 들어, 격리 수준이 READ_COMMITTED인 InnoDB 테이블에서 데이터 변경은 다음 과정으로 진행된다.
```mysql
mysql> CREATE TABLE member (
    m_id INT NOT NULL,
    m_name VARCHAR(20) NOT NULL,
    m_area VARCHAR(100) NOT NULL,
    PRIMARY KEY (m_id),
    INDEX ix_area (m_area)
);
mysql> INSERT INTO member (m_id, m_name, m_area) VALUES (12, '홍길동', '서울');
mysql> COMMIT;
mysql> UPDATE member SET m_area='경기' WHERE m_id=12;
```
INSERT 문장이 실행되면 InnoDB의 디스크의 데이터 파일과 버퍼 풀에 12, '홍길동', '서울'이라는 값으로 레코드가 올라간다. UPDATE 문장이 실행되면 커밋 실행 여부와 관계없이 InnoDB의 버퍼 풀은 새로운 값 '경기'로 업데이트된다. 디스크의 데이터 파일에는 Write 스레드에 의해 '경기'로 업데이트돼 있을 수도 있고 아닐 수도 있다. 아직 COMMIT이나 ROLLBACK이 되지 않은 상태에서 다른 사용자가 다음 같은 쿼리로 작업 중인 레코드를 조회하면 어디에 있는 데이터를 가져갈까?  

```mysql
mysql> SELECT * FROM member WHERE m_id=12;
```

답은 MySQL 서버의 transaction_isolation 시스템 변수에 설정된 격리 수준에 따라 다르다는 것이다. 격리 수준이 READ_UNCOMMITTED인 경우 버퍼 풀의 데이터를 반환한다. 그렇지 않고 READ_COMMITED, REPEATABLE_READ, SERIALIZABLE인 경우 아직 커밋되지 않았기 때문에 언두 영역의 데이터를 반환한다. 이 과정을 DBMS에서는 MVCC라고 부른다. 즉, 회원 번호가 12인 레코드 하나에 대해 2개의 버전이 유지되고 상황에 따라 리턴되는 결과가 달라지는 구조다.  

```mysql
mysql> UPDATE member SET m_area='경기' WHERE m_id=12;
mysql> COMMIT;
```
UPDATE 이후 COMMIT 명령을 실행하면 InnoDB는 업데이트 결과 상태를 영구적인 데이터로 만들어 버린다. 반면 ROLLBACK을 실행하면 InnoDB는 언두 영역에 있는 (백업된) 데이터를 버퍼 풀로 다시 복구하고 언두 영역의 내용을 삭제한다. COMMIT이 된다고 언두 영역의 데이터가 항상 바로 삭제되는 것은 아니다. 이 언두 영역을 필요로 하는 트랜잭션이 더는 없을 때 비로소 삭제된다.  

### 4. Non-Locking Consistent Read
InnoDB 스토리지 엔진은 MVCC 기술을 이용해 잠금을 걸지 않고도 읽기 작업을 수행할 수 있다. 격리 수준이 SERIALIZABLE이 아닌 READ_UNCOMMITTED, READ_COMMITTED, REPEATABLE_READ 수준인 경우 INSERT와 연결되지 않은 순수한 SELECT 작업은 항상 즉시 실행된다. 이를 '잠금 없는 일관된 읽기'라고 부른다. 트랜잭션이 길어지면 MySQL 서버가 느려지거나 문제가 발생할 수 있다. 이는 '잠금 없는 일관된 읽기'를 위해 언두 영역에서 관리하는 데이터가 오랫동안 관리되어야 하기 때문에 발생하는 문제다. 따라서 트랜잭션이 시작됐다면 가능한 한 빨리 롤백이나 커밋을 통해 트랜잭션을 완료하는 것이 좋다.  

### 5. 자동 데드락 감지
InnoDB 스토리지 엔진은 내부적으로 데드락이 발생했는지 체크하기 위해 잠금 대기 목록을 그래프(Wait-for List) 형태로 관리한다. 데드락을 감지하는 스레드를 가지고 있어서 주기적으로 잠금 대기 그래프를 검사해서 데드락에 빠진 트랜잭션을 찾아서 그중 하나를 강제 종료한다. 일반적으로 트랜잭션의 언두 로그 레코드를 적게 가진 트랜잭션을 강제 종료(롤백)시킨다. 트랜잭션이 언두 레코드를 적게 가졌다는 뜻은 롤백을 해도 언두 처리를 해야 할 내용이 적다는 것이다. 또한, 강제 롤백으로 인한 MySQL 서버의 부하도 덜 유발하기 때문에 언두 로그 레코드의 양을 기준으로 선택한다.  

InnoDB 스토리지 엔진은 상위 레이어인 MySQL 엔진에서 관리되는 (LOCK TABLES로 잠긴) 테이블 잠금은 볼 수 없어서 데드락 감지가 불확실할 수도 있다. innodb_table_locks 시스템 변수를 활성화하면 테이블 레벨의 잠금까지 감지할 수 있게 되니 특별한 이유가 없다면 innodb_table_locks 시스템 변수를 활성화하는 것이 좋다.  

일반적인 서비스에서는 데드락 감지 스레드가 잠금 목록을 검사해서 데드락을 찾아내는 작업이 크게 부담되지 않는다. 하지만 매우 많은 트랜잭션이 동시에 실행되고 각 트랜잭션에서 가지고 있는 잠금의 개수가 많아지면 데드락 감지 스레드때문에 성능이 저하될 수 있다. 구글에서 이런 문제가 발견되었고 오라클에서는 이를 개선하기 위해 MySQL에서 데드락 감지 스레드를 비활성화할 수 있는 기능을 추가했다. innodb_deadlock_detect을 OFF로 설정해서 비활성화 하는 경우, innodb_lock_wait_timeout을 기본값인 50초보다 훨씬 낮은 시간으로 변경해서 사용할 것을 권장한다.

### 6. 자동화된 장애 복구
MySQL 서버가 시작될 때 완료되지 못한 트랜잭션이나 디스크에 일부만 기록된 (Partial write) 데이터 페이지 등에 대한 일련의 복구 작업이 자동으로 진행된다. 따라서 InnoDB 테이블에서 데이터 파일이 손상되거나 그로 인해 MySQL 서버가 시작하지 못하는 경우는 거의 발생하지 않는다. 하지만 MySQL 서버와 무관하게 디스크나 서버 하드웨어 이슈로 자동 복구를 하지 못하는 경우도 있다. 이 때 innodb_force_recovery 시스템 변수를 설정해서 MySQL 서버가 시작될 때 InnoDB 스토리지 엔진이 데이터 파일이나 로그 파일의 손상 여부 검사 과정을 선별적으로 진행할 수 있게 설정해줘야 한다.

## 참고
Real MySQL 8.0 1권 4장  