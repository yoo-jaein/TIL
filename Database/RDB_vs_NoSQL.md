# RDB vs NoSQL

## Relational Databases
1. 정해진 스키마에 따라 데이터를 저장해야 하기 때문에 명확한 데이터 구조를 보장한다.
2. SQL을 통해 데이터를 다룰 수 있다.
3. 데이터 무결성을 보장한다. 즉, 중복된 데이터가 없도록 관리된다.
4. 테이블 사이에 관계가 형성되어 있어서 시스템이 커질 경우 조인문이 많아지고 복잡한 쿼리가 발생할 수 있다.
5. 대체로 스케일 업만 지원한다.

> 관계를 맺고 있는 데이터가 자주 업데이트되는 시스템. 명확한 스키마가 필요한 시스템.

## NoSQL
1. 테이블에 스키마가 정해져있지 않아서 데이터 저장이 비교적 자유롭다.
2. 테이블간 관계를 정의하지 않고 조인이 불가능하다.
3. 스케일 업, 스케일 아웃 모두 가능하다.
    1. Scale Up : 서버에 CPU, RAM 등을 추가하거나 고성능의 서버로 교환하는 방법. 서버 한 대에 모든 부하가 집중되고 모든 데이터를 처리한다.
    2. Scale Out : 서버를 여러 대 추가해서 시스템을 확장하는 방법. 서버가 여러 대가 되기 때문에 각 서버에 걸리는 부하를 균등하게 해주는 로드밸런싱이 필수적이다. 서버 한 대가 장애로 다운되더라도 다른 서버로 서비스 제공이 가능하다는 장점이 있다. 
4. 데이터 무결성이 보장되지 않아서 중복된 데이터가 존재할 수 있다.
5. 중복된 데이터가 변경될 때 모든 컬렉션에 업데이트를 수행해야 한다.
6. key(partition key)에 대한 입출력만 지원한다.

> 데이터의 업데이트가 자주 일어나지 않는 시스템. 정확한 데이터 스키마를 알 수 없거나, 언제든지 변경 또는 확장될 수 있는 시스템. 대량의 데이터를 저장하여 데이터베이스의 스케일 아웃이 필요한 시스템. 

## 왜 RDB는 스케일 아웃에 불리할까?
RDB에서 분산을 하려면 테이블을 수평으로 분할하거나 수직으로 분할한다. 만약 한 테이블이 여러 서버에 분산되어 있고 해당 테이블을 다른 테이블과 조인하려고 한다면, 분산된 모든 테이블을 읽어야 한다. 따라서 RDB는 스케일 아웃이 불리하다.

## NoSQL이 RDB보다 빠를까?
아니다. 데이터베이스의 속도는 컨텍스트에 달려 있다.  
RDB는 조인, 쿼리, 업데이트 등에 대해 NoSQL보다 빠르다. 그러나 분할되지 않은 특정 데이터에 대해 읽기 또는 쓰기 작업을 수행하는 것은 NoSQL에서 더 빠르다.  

## NoSQL이 빅데이터 애플리케이션에 더 적합할까?
그렇다.  
NoSQL은 빅데이터에 적합한 동적(유연한) 스키마를 가지고 있다. 또한, 스케일 아웃이 가능하기 때문에 매우 많은 양의 데이터를 저장할 수 있다.

## 참고
https://www.geeksforgeeks.org/sql-vs-nosql-which-one-is-better-to-use/