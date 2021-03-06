# Index

## 데이터베이스 인덱스
DBMS에서 인덱스는 데이터의 저장(INSERT, UPDATE, DELETE) 성능을 희생하고 그 대신 데이터의 읽기 속도를 높이는 기능이다. 인덱스는 책의 맨 끝에 있는 찾아보기 또는 색인에 비유된다. 책의 내용을 모두 확인해서 원하는 내용을 발견하려면 시간이 오래 걸린다. DBMS도 데이터베이스 테이블의 모든 데이터를 검색해서 원하는 결과를 가져오려면 시간이 오래 걸린다. 그래서 하나 이상의 컬럼의 값과 해당 레코드가 저장된 주소를 키와 값의 쌍(key-value)으로 삼아서 인덱스를 만들어 둔다. 책의 색인이 'ㄱ', 'ㄴ', 'ㄷ' 순으로 정렬되어 있는 것 처럼 DBMS의 인덱스도 컬럼의 값을 미리 정렬해서 보관한다. 데이터가 추가될 때마다 항상 값을 정렬해야 하므로 INSERT, UPDATE, DELETE 문장의 처리가 느려진다. 하지만 SELECT 문장은 아주 빠르게 처리할 수 있다.

## 인덱스의 분류
인덱스는 성격에 따라 여러 가지로 분류될 수 있다.

- 레코드를 대표하는(식별하는) 컬럼의 값으로 만들어진 인덱스를 ```프라이머리 키```라 한다. ```프라이머리 키```는 NULL 값과 중복을 허용하지 않는다. ```프라이머리 키```를 제외한 나머지 모든 인덱스는 ```세컨더리 인덱스```로 분류된다. 
- 데이터를 저장하는 방식으로 구분하면 대표적으로 시중의 RDBMS에서 많이 사용되는 ```B-Tree 인덱스```와 ```Hash 인덱스```로 구분할 수 있다. 
- 인덱스(안에 있는 컬럼 키 값)가 유니크한지 아닌지로 구분하면 ```유니크 인덱스```와 ```유니크하지 않은 인덱스```로 구분할 수 있다.
- ```1개의 컬럼만 포함된 인덱스```와 2개 이상의 컬럼을 포함하는 ```다중 컬럼 인덱스```로 구분할 수 있다.

## Hash 인덱스
- 컬럼의 값으로 해시값을 계산해서 인덱싱하는 알고리즘이다.
- 매우 빠른 검색을 지원하지만 값을 변형해서 인덱싱하기 때문에 prefix 일치와 같이 값의 일부만 검색하거나 범위를 검색할 때는 Hash 인덱스를 사용할 수 없다.
- 주로 메모리 기반의 데이터베이스에서 많이 사용한다.

## B-Tree 인덱스
- B-Tree 인덱스는 가장 일반적으로 사용되는 인덱스 알고리즘이다. B-Tree의 B는 Balanced를 의미한다. B-Tree 인덱스는 컬럼의 값을 변형하지 않고 원래의 값을 이용해 인덱싱한다.
- B-Tree는 트리 구조 최상위에 하나의 ```루트 노드(Root node)```가 존재하고 가장 하위에 ```리프 노드(Leaf node)```가 붙어있다. 루트 노드도 아니고 리프 노드도 아닌 중간의 노드를 ```브랜치 노드(Branch node)```라고 한다.
- 인덱스는 테이블의 키 컬럼만 가지고 있으므로 나머지 컬럼을 읽으려면 데이터 파일에서 해당 레코드를 찾아야 한다. 이를 위해 인덱스의 리프 노드는 실제 데이터 레코드의 주솟값을 가진다. 참고로 인덱스의 키 값은 모두 정렬되어 있지만, 데이터 파일의 레코드는 임의의 순서로 저장되어 있다. 레코드가 삭제되어 빈 공간이 생기면 그다음의 INSERT는 가능한 한 삭제된 공간을 재활용하도록 DBMS가 설계되어 있다.
- InnoDB 테이블에서 인덱스를 통해 레코드를 읽을 때는 데이터 파일을 바로 찾아가지 못한다. 인덱스에 저장되어 있는 프라이머리 키 값을 이용해 프라이머리 키 인덱스를 한 번 더 검색한 후, 프라이머리 키 인덱스의 리프 페이지에 저장돼 있는 레코드를 읽는다. InnoDB 스토리지 엔진에서는 모든 세컨더리 인덱스 검색에서 데이터 레코드를 읽기 위해서는 반드시 프라이머리 키를 저장하고 있는 B-Tree를 다시 한번 검색해야 한다.
- 리프 노드는 인덱스 키 값과 그 키 값에 해당하는 테이블 레코드를 찾아가기 위한 주소 정보(ROWID)를 가지며 같은 키 값일 때는 ROWID 순으로 정렬된다.
- 인덱스 키 값 순으로 정렬되어 있기 때문에 범위 스캔(Range Scan)이 가능하고, 오름차순, 내림차순 스캔이 가능한 양방향 연결 리스트 구조다.
- OLTP(Online Transaction Processing) 실시간으로 데이터 입력과 수정이 일어나는 환경에 많이 사용된다.

## B-Tree 인덱스의 키 추가, 삭제, 변경, 검색
### 인덱스 키 추가
B-Tree에 새로운 인덱스 키 값이 저장될 때는 먼저 적절한 저장 위치를 검색해야 한다. 위치가 결정되면 레코드의 키 값과 대상 레코드의 주소 정보를 B-Tree의 리프 노드에 저장한다. 리프 노드가 꽉 차서 더는 저장할 수 없을 때는 리프 노드가 분리돼야 한다. 이 때 상위 브랜치 노드까지 처리의 범위가 넓어지기 때문에 B-Tree는 상대적으로 새로운 키를 추가하는 쓰기 작업에 비용이 많이 드는 것으로 알려졌다. InnoDB 스토리지 엔진의 경우 INSERT 문장이 실행되면 인덱스 키 추가 작업을 지연시켜서 나중에 처리할 수도 있다. 하지만 프라이머리 키나 유니크 인덱스의 경우 중복 체크가 필요하기 때문에 즉시 처리한다.  

### 인덱스 키 삭제
B-Tree의 키 값이 삭제되는 경우는 간단하다. 해당 키 값이 저장된 B-Tree의 리프 노드를 찾아서 삭제 마킹을 하면 완료된다. 마킹 작업은 디스크 쓰기가 필요하다. InnoDB 스토리지 엔진에서는 이 작업 또한 버퍼링되어 지연 처리할 수 있다.

### 인덱스 키 변경
인덱스의 키 값은 그 값에 따라 저장될 리프 노드의 위치가 결정된다. 따라서 B-Tree의 키 값이 변경되는 경우에는 먼저 키 값을 삭제한 후, 다시 새로운 키 값을 추가하는 형태로 처리된다.

### 인덱스 키 검색
인덱스를 검색하는 작업은 B-Tree의 루트 노드부터 시작해 브랜치 노드를 거쳐 최종 리프 노드까지 이동하면서 비교 작업을 수행하는데, 이 과정을 ```트리 탐색```이라고 한다. 이 검색은 100% 일치하거나 값의 앞부분(left-most part)만 일치하는 경우에 사용할 수 있다.

## B-Tree 인덱스에서 성능에 영향을 미치는 요소
### 1. 인덱스 키 값의 크기
InnoDB 스토리지 엔진에서 디스크에 데이터를 읽고 쓰는 가장 기본 최소 단위를 페이지(Page) 또는 블록(Block)이라 한다. 페이지는 InnoDB 스토리지 엔진의 버퍼 풀에서 데이터를 버퍼링하는 기본 단위이기도 하다. 인덱스도 페이지 단위로 된다. DBMS의 B-Tree는 자식 노드의 개수가 가변이다. 가질 수 있는 자식 노드의 개수는 인덱스의 페이지 크기와 키 값의 크기에 따라 결정된다. 하나의 인덱스 페이지는 인덱스 키와 자식 노드 주소 영역의 여러 묶음으로 구성된다. 인덱스 키 값의 크기가 커지면 한 페이지에 저장할 수 있는 키의 개수는 적어진다. 결국 인덱스를 구성하는 키 값의 크기가 커지면 디스크로부터 읽어야 하는 횟수가 늘어나고, 그만큼 느려진다는 것을 의미한다. 또한, 인덱스 키 값의 길이가 길어진다는 것은 전체적인 인덱스의 크기가 커진다는 것을 의미한다. 인덱스를 캐시해 두는 InnoDB의 버퍼 풀이나 MyISAM의 키 캐시 영역은 크기가 제한적이기 때문에 하나의 레코드를 위한 인덱스 크기가 커지면 커질수록 메모리에 캐시해 둘 수 있는 레코드 수는 줄어든다. 그렇게 되면 자연스럽게 메모리 효율이 떨어진다.  

### 2. 유니크한 인덱스 키 값의 개수
인덱스에서 선택도(Selectivity) 또는 기수성(Cardinality)는 모든 인덱스 키 값 가운데 유니크한 값의 수를 의미한다. 인덱스 키 값 가운데 중복된 값이 많을수록 기수성과 선택도는 떨어진다. 키 값이 유니크한 인덱스가 많아서 선택도가 높을수록 검색 대상이 줄어들기 때문에 빠르게 처리된다.

### 3. 읽어야 하는 레코드의 건수
인덱스를 통해 테이블의 레코드를 읽는 것은 인덱스를 거치지 않고 바로 테이블의 레코드를 읽는 것 보다 높은 비용이 든다. 일반적인 DBMS의 옵티마이저에서는 인덱스를 통해 레코드 1건을 읽는 것이 테이블에서 직접 레코드를 1건 읽는 것보다 4~5배 정도 비용이 더 많이 드는 것으로 예측한다. 즉, 인덱스를 통해 읽어야 할 레코드의 건수가 전체 테이블 레코드의 20\~25%를 넘어서면 인덱스를 이용하지 않고 테이블을 모두 직접 읽어서 필요한 레코드만 필터링하는 방식으로 처리하는 것이 효율적이다.

## B-Tree 인덱스를 통한 데이터 읽기
MySQL이 인덱스를 이용해서 데이터를 읽는 방식에는 대표적으로 인덱스 레인지 스캔, 인덱스 풀 스캔, 루스 인덱스 스캔이 있다.

### 1. 인덱스 레인지 스캔
```text
1. 인덱스에서 조건을 만족하는 값이 저장된 위치를 찾는다. (인덱스 탐색)
2. 탐색된 위치부터 필요한 만큼 인덱스를 차례대로 쭉 읽는다. (인덱스 스캔)
3. 읽어 들인 인덱스 키와 레코드 주소를 이용해 레코드가 저장된 페이지를 가져오고 최종 레코드를 읽어 온다.
	3-1. 커버링 인덱스로 처리되는 쿼리는 디스크의 레코드를 읽지 않아도 되기 때문에 랜덤 I/O가 상당히 줄어들고 성능은 그만큼 빨라진다.
```

B-Tree 인덱스에서 가장 일반적인 읽기 방식이다. 검색해야 할 인덱스의 범위가 결정됐을 때 사용한다. 루드 노드에서부터 비교를 시작해 브랜치 노드를 거치고 최종적으로 리프 노드까지 들어가서 필요한 레코드의 시작 지점을 찾는다. 시작 위치를 찾으면 그때부터 리프 노드의 레코드를 순서대로(오름차순 또는 내림차순) 읽는다. 차례대로 쭉 읽기 때문에 스캔이라고 표현한다. 스캔하다가 리프 노드의 끝까지 읽으면 리프 노드 간의 링크를 이용해 다음 리프 노드를 찾아서 계속 스캔한다. 스캔을 종료할 위치에 다다르면 지금까지 읽은 레코드를 사용자에게 반환하고 쿼리를 끝낸다.   

B-Tree 인덱스의 리프 노드를 스캔하면서 실제 데이터 파일의 레코드를 읽어와야 하는 경우가 많다. 인덱스의 리프 노드에서 검색 조건에 일치하는 건들은 데이터 파일에서 레코드를 읽어오는 과정이 필요하다. 이때 레코드 한 건마다 랜덤 I/O가 한 번씩 일어난다. 그래서 인덱스를 통해 데이터 레코드를 읽는 작업은 비용이 많이 드는 작업으로 분류된다.

### 2. 인덱스 풀 스캔
인덱스 레인지 스캔과 다르게 인덱스의 처음부터 끝까지 모두 읽는 방식이다. 인덱스 레인지 스캔보다는 느리지만 테이블 풀 스캔보다는 효율적이다. 인덱스에 포함된 컬럼만으로 쿼리를 처리할 수 있는 경우 테이블의 레코드를 읽을 필요가 없기 때문이다. 인덱스의 전체 크기는 테이블 자체의 크기보다는 훨씬 작으므로 인덱스 풀 스캔은 테이블 전체를 읽는 것보다는 적은 디스크 I/O로 쿼리를 처리할 수 있다.

### 3. 루스 인덱스 스캔
오라클과 같은 DBMS에서 인덱스 스킵 스캔이라고 하는 기능과 작동 방식이 비슷하다. 말 그대로 느슨하게, 듬성듬성하게 인덱스를 읽는 것을 의미한다. 중간에 필요하지 않은 인덱스 키 값은 무시하고 다음으로 넘어가는 형태로 처리한다. 일반적으로 GROUP BY 또는 MAX(), MIN() 함수에 대해 최적화하는 경우 사용된다.

### 4. 인덱스 스킵 스캔
루스 인덱스 스캔은 GROUP BY 작업을 처리하기 위해 주로 사용되었다. MySQL 8.0에 도입된 인덱스 스킵 스캔은 WHERE 조건절의 검색을 위해 사용 가능하도록 용도가 훨씬 넓어진 것이다.

## 클러스터 인덱스
- 인덱스에서 클러스터드는 비슷한 것들을 묶어서 저장하는 형태로 구현
- 주로 비슷한 값들을 동시에 조회하는 경우가 많다는 점에서 착안
- 클러스터드 인덱스는 테이블의 기본키에 대해서만 적용된다.
- 기본키 값이 비슷한 레코드끼리 묶어서 저장한다.

#### 클러스터링
- 디스크로부터 데이터를 읽어오는 시간을 줄이기 위해 조인이나 자주 사용되는 테이블의 데이터를 디스크의 같은 위치에 저장시키는 방법
- 데이터 조회 성능을 향상시키지만 저장, 삭제, 수정 또는 테이블 풀 스캔 성능을 감소시킴

#### 장점
- 그룹된 컬럼 데이터 행들이 같은 데이터 Block에 저장되기 때문에 디스크 I/O를 줄임
- 클러스터된 테이블 사이에 조인이 발생할 경우 처리 시간이 단축
- 클러스터 키 열을 공유해 한 번만 저장하므로 저장 영역의 사용을 줄임

#### 클러스터하기 좋은 테이블
주로 조회가 자주 발생하고 수정이 거의 발생하지 않는 테이블, 컬럼 안의 많은 중복 데이터를 가지는 테이블, 자주 Join되는 테이블

#### 클러스터 키가 되기 좋은 컬럼
데이터 값의 범위가 큰 컬럼, 테이블 간의 조인에 사용되는 컬럼

#### 클러스터 키가 되기 나쁜 컬럼
특정 데이터 값이 적은 칼럼, 자주 데이터 수정이 발생하는 컬럼

## 참고
Real MySQL 8.0 1권 8장  