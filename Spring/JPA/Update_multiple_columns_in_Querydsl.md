# Update multiple columns in Querydsl
JPA Querydsl에서 Update를 할 때 여러 컬럼을 set()하려면 어떻게 해야 할까?

## Update문
```java
long count = queryFactory
    .update(member)
    .set(member.username, "비회원")
    .where(member.age.lt(28))
    .execute();
```
Querydsl의 Update문은 update(), where(), set(), execute() 등의 메서드로 이루어진다. where()은 필수가 아니다.

## 여러 개의 set()으로 Update하기
```java
long count = queryFactory
    .update(member)
    .set(member.username, "비회원")
    .set(member.status, 0)
    .setNull(member.address)        
    .where(member.age.lt(28))
    .execute();
```
Update를 할 때 여러 컬럼을 변경하고 싶다면 .set()을 여러 번 체이닝하여 사용하면 된다. 만약 어떤 컬럼에 null을 넣고 싶다면 setNull() 메서드를 사용하면 된다.

## 주의!
JPQL 배치와 마찬가지로, Update 배치 쿼리는 영속성 컨텍스트에 있는 엔티티를 무시하고 실행되기 때문에 실행한 뒤 영속성 컨텍스트를 초기화하는 것이 안전하다.  

## 참고
http://www.querydsl.com/static/querydsl/4.0.4/reference/html_single/  
https://www.baeldung.com/intro-to-querydsl#4-modifying-data  