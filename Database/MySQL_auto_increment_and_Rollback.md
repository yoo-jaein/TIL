# MySQL auto_increment and Rollback
MySQL의 auto_increment 컬럼과 트랜잭션 롤백

## auto_increment
MySQL에서 트랜잭션을 시작하고 데이터를 insert한 뒤, 롤백시킨다면 데이터는 사라지지만 auto_increment 컬럼의 값은 되돌아가지 않는다.  

그 이유는 auto_increment의 동작 방식에 있다. auto_increment의 매커니즘은 트랜잭션의 범위 바깥에서 동작해야 한다. 왜냐하면 다른 누군가가 내가 트랜잭션을 끝내기 전에 동일한 테이블에 데이터를 insert할 수 있기 때문이다. 그래서 auto_increment 카운터는 롤백되지 않으며, 트랜잭션의 원자성(atomicity)에 위배된다. 그래서 auto_increment를 사용하면 때때로 값에 손실되는 범위가 생기기도 한다.   

## 결론
auto_increment는 일반적으로 기본 키에 적용하는데, 기본 키 값은 고유해야 하지만 연속적일 필요는 없다. 따라서 auto_increment로 생성된 값을 줄이거나 바꿀 필요가 없다. 

## 참고
https://stackoverflow.com/questions/14758625/mysql-auto-increment-columns-on-transaction-commit-and-rollback  
https://stackoverflow.com/questions/449346/mysql-auto-increment-does-not-rollback  