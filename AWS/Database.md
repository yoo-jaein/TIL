#Database
RDS, Aurora, DynamoDB, Neptune, ElastiCache, Redshift

##RDS
널리 사용되는 6개의 데이터베이스 엔진 중 하나를 선택하여 실행되는 관계형 DB.  
Aurora, MySQL, MariaDB, Oracle, MSSQL, PostgreSQL 엔진을 사용할 수 있다.  
내부에서 EC2 기반으로 동작한다.  
백업 기능이 있다.  

##Aurora
MySQL 및 PostgreSQL과 호환되는 관계형 DB.   
클라우드에 맞게 구축되었다.  
Failover 시간이 매주 짧다.  
특정 지점으로 복구시키는 리와인드 기능이 있다.  

##DynamoDB
NoSQL DB.  
어떤 규모에서든 빠르고 유연하게 동작한다.  
쿼리의 지연시간이 짧다. 항상 10ms 속도를 보장받는다.  
그래서 비싸다.  

##ElastiCache
Redis key-value 스토어.  
인메모리 데이터 스토어 서비스이다.  

##Redshift
데이터 웨어하우스.  
대량의 데이터를 넣어 두고 이 데이터에 분석용 쿼리를 날려서 처리하는데 특화되었다.  

##Neptune
클라우드용 그래프 DB.  
관계를 가지고 쿼리한다.  

##DMS
Data Migration Service  
최소한의 가동 중지로 데이터베이스를 AWS로 마이그레이션한다.  
어딘가의 원본 DB를 읽어다가 타겟 DB로 쏴주는 역할을 한다.  
간편한 사용, 저렴한 비용, 안정성.

##특징
내부적으로 EC2 위에 소프트웨어가 설치되어 동작하는 방식이다.
일회성 마이그레이션(중단시킨 뒤 데이터를 이관)  
라이브 마이그레이션(중단없이 쭉 업데이트하다가 마지막에 새로운 데이터베이스를 가리키도록 설정)
