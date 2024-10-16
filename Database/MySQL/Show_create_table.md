# Show create table
MySQL에서 테이블의 생성 쿼리 알아내기

## SQL문
```mysql-sql
--show create table [테이블명];
show create table member;
```

## 결과
|   | Table  | Create Table                                    |
|---|--------|-------------------------------------------------|
| ▶ | member | CREATE TABLE 'member' ( 'member_id' int(11) ... |