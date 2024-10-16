# MySQL Engine Architecture

## MySQL 서버
MySQL 서버는 크게 다음의 두 엔진으로 구분할 수 있다. 
- MySQL 엔진
- 스토리지 엔진 (MySQL 서버에서 기본으로 제공되는 InnoDB 스토리지 엔진, MyISAM 스토리지 엔진 등)

## MySQL 엔진
MySQL 엔진은 요청된 SQL 문장을 분석하거나 최적화하는 일을 한다. MySQL 엔진은 클라이언트로부터의 접속 및 쿼리 요청을 처리하는 커넥션 핸들러와 SQL 파서, 전처리기, 옵티마이저로 이루어진다. 

## 스토리지 엔진
스토리지 엔진은 실제 데이터를 디스크 스토리지에 저장하거나 디스크 스토리지로부터 데이터를 읽어오는 역할을 수행한다. MySQL 서버에서 MySQL 엔진은 하나지만 스토리지 엔진은 여러 개를 동시에 사용할 수 있다. 테이블이 사용할 스토리지 엔진을 지정하면 이후 해당 테이블의 모든 읽기 작업과 변경 작업은 정의된 스토리지 엔진이 처리한다.
```mysql
CREATE TABLE test_table (fd1 INT, fd2 INT) ENGINE=INNODB;
```

test_table은 InnoDB 스토리지 엔진을 사용하도록 정의했다. 각 스토리지 엔진은 성능 향상을 위해 키 캐시(MyISAM 스토리지 엔진)나 InnoDB 버퍼 풀(InnoDB 스토리지 엔진)과 같은 기능을 내장하고 있다. 

## 핸들러 API
MySQL 엔진의 쿼리 실행기에서 데이터를 쓰거나 읽어야 할 때는 각 스토리지 엔진에 쓰기 또는 읽기를 요청하는데, 이러한 요청을 핸들러(Handler) 요청이라 하고, 여기서 사용되는 API를 핸들러 API라고 한다. 이 핸들러 API 조건을 만족시켜 스토리지 엔진을 직접 구현할 수도 있다.

## MySQL 스레딩 구조
MySQL 서버는 프로세스 기반이 아니라 스레드 기반으로 작동한다. 스레드는 크게 포그라운드(Foreground) 스레드와 백그라운드(Background) 스레드로 구분한다. 이는 MySQL 커뮤니티 에디션에서 전통적으로 가지고 있던 스레드 모델이며 MySQL 엔터프라이즈 에디션과 Percona MySQL 서버에서는 스레드 풀 모델을 사용할 수도 있다. 실행 중인 스레드의 목록은 performance_schema 데이터베이스의 threads 테이블을 통해 확인할 수 있다.

```mysql
SELECT thread_id, name, type, processlist_user, processlist_host FROM performance_schema.threads ORDER BY type, thread_id;
```

### 포그라운드 스레드
포그라운드 스레드(클라이언트 스레드)는 최소한 MySQL 서버에 접속된 클라이언트의 수만큼 존재하며, 주로 각 클라이언트 사용자가 요청하는 쿼리 문장을 처리한다. 데이터를 MySQL의 데이터 버퍼나 캐시로부터 가져오며, 버퍼나 캐시에 없는 경우에는 직접 디스크의 데이터나 인덱스 파일로부터 데이터를 읽어와서 작업을 처리한다. MyISAM 테이블은 일반적으로 디스크 쓰기 작업까지 포그라운드 스레드가 처리한다. 그러나 InnoDB 테이블은 데이터 버퍼나 캐시까지만 포그라운드 스레드가 처리하고 나머지 버퍼로부터 디스크까지 기록하는 작업은 백그라운드 스레드가 처리한다.  

클라이언트 사용자가 작업을 마치고 커넥션을 종료하면 해당 커넥션을 담당하던 스레드는 다시 스레드 캐시(Thread cache)로 되돌아간다. 이때 이미 스레드 캐시에 일정 개수(thread_cache_size 시스템 변수) 이상의 대기 중인 스레드가 있으면 스레드 캐시에 넣지 않고 스레드를 종료시켜 일정 개수의 스레드만 스레드 캐시에 존재하게 한다.

### 백그라운드 스레드
InnoDB의 경우 백그라운드 스레드에서 다음과 같은 작업을 수행한다.
- 인서트 버퍼(Insert Buffer)를 병합하기
- 로그를 디스크로 기록하기
- InnoDB 버퍼 풀의 데이터를 디스크에 기록하기
- 데이터를 버퍼로 읽어오기
- 잠금이나 데드락을 모니터링하기

## 참고
https://www.oreilly.com/library/view/high-performance-mysql/9780596101718/ch01.html  
Real MySQL 8.0 1권 4장