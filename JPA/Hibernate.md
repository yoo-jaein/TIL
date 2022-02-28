# Hibernate

## 하이버네이트
Hibernate는 자바의 ORM(Object-Relational Mapping) 프레임워크다. 자바 개발자는 객체를 사용하여 코드를 작성할 수 있고 Hibernate는 데이터베이스에서 로드된 데이터에서 이러한 객체를 생성하고 업데이트를 데이터베이스에 다시 저장하는 것을 처리한다.

## 주요 메서드
### save()와 persist()의 차이
```java
// package org.hibernate.Session;
Serializable save(Object object);
```
- save() : 직렬화 가능한 객체를 반환한다.

```java
// package org.hibernate.Session;
void persist(Object object);
```
- persist() : 파라미터로 들어오는 원본 객체를 수정한다. 아무 것도 반환하지 않는다. 

### save()와 saveOrUpdate()의 차이
- save() : 레코드를 Insert만 할 수 있다.

```java
// package org.hibernate.Session;
void saveOrUpdate(Object object);
```
- saveOrUpdate() : 레코드를 Insert 또는 Update할 수 있다.

## 하이버네이트의 영속성 객체 상태 
1. transient : 자바 프로그램에서 생성되었지만 Hibernate의 Session과 연결되지 않은 객체는 transient 상태에 있다고 한다.
2. persistent : 반면에, Hibernate의 Session과 연결된 객체를 persistent 객체라고 한다.
3. detached : 이전에 Hibernate의 Session과 연결되었지만 현재는 연결되지 않은 객체를 detached 객체라고 한다.

save()나 persist() 메서드를 호출하면 해당 객체를 데이터베이스에 저장하고 persistent 상태로 만들 수 있다.

## 하이버네이트에서 사용하는 캐시
[1차 캐시와 2차 캐시](https://github.com/yoo-jaein/TIL/blob/main/JPA/First_level_cache_and_Second_level_cache_in_Hibernate.md)

## Session과 SessionFactory의 차이
- Session : Session 객체는 애플리케이션의 단일 스레드 내에서 사용되도록 만들어졌다. (스레드 세이프하지 않다.) 수명이 짧고 1차 캐시를 제공한다.
- SessionFactory : SessionFactory는 불변이며 애플리케이션에서 단 하나의 단일 인스턴스를 갖는다. (스레드 세이프하다.) Session 객체를 생성하는 데 사용되며 여러 Session에서 저장한 SQL 쿼리를 저장하여 캐싱도 제공한다. 2차 캐시는 SessionFactory 수준에서 유지 관리된다. Hibernate가 실행되는 동안 지속된다.

## 참고
https://www.java67.com/2016/02/top-20-hibernate-interview-questions.html  