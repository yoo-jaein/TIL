# Refactor JPQL to Querydsl
@Query에 문자로 작성된 JPQL을 Querydsl 기반의 코드로 변환하는 리팩토링 작업

## Repository 구조
- public interface MemberRepositoryCustom : Querydsl로 내가 직접(custom) 구현할 메서드를 정의하는 곳  
- public interface MemberRepository extends JpaRepository<Member, Long> : 현재 리팩토링 대상인 @Query JPQL이 들어있고 Spring Data JPA 메서드가 정의되는 곳   
- public class MemberRepositoryImpl extends QuerydslRepositorySupport implements MemberRepositoryCustom : Custom repository를 구현하여 실제로 Querydsl 코드가 작성되는 곳  

## 개선 방향
1. 현재 Repository 인터페이스의 메서드 중 @Query 어노테이션이 붙은 것을 Custom 인터페이스로 옮긴다.  
   - 이 때, 더이상 필요 없는 어노테이션(@Param, @Modifying 등)이 있는지 체크하고 최소한의 것들만 함께 옮긴다.  
2. 옮긴 메서드들을 Impl 클래스에서 오버라이드하고 JPQL을 Querydsl 코드로 구현한다.  
3. Repository 인터페이스에 @Query 어노테이션이 붙은 메서드를 제거한 후 Custom 인터페이스를 extends 시킨다.  

## 기존 코드
```java
// MemberRepository.java

public interface MemberRepository extends JpaRepository<Member, Long> { 
    @Query("select m from Member m where m.contact = :contact order by m.username asc") 
    List<Member> findByContact(@Param("contact") String contact);   
}
```

```java
// MemberRepositoryCustom.java

public interface MemberRepositoryCustom {
   List<Member> findByContact(String contact);
}
```

```java
// MemberRepositoryImpl.java

public class MemberRepositoryImpl implements MemberRepositoryCustom {
    
}
```

## 공식 레퍼런스
http://www.querydsl.com/static/querydsl/4.4.0/reference/html_single/  

## 결과 코드
```java
// MemberRepository.java

public interface MemberRepository extends JpaRepository<Member, Long>, MemberRepositoryCustom {
}
```

```java
// MemberRepositoryCustom.java

public interface MemberRepositoryCustom {
   List<Member> findByContact(String contact);
}
```

```java
// MemberRepositoryImpl.java
@RequiredArgsConstructor
public class MemberRepositoryImpl implements MemberRepositoryCustom {
    
   private final JPAQueryFactory jpaQueryFactory;
   
   @Override
   public List<Member> findByContact(String contact) {
       return jpaQueryFactory
               .selectFrom(member)
               .where(member.contact.eq(contact))
               .orderBy(member.username.asc())
               .fetch();
   } 
}
```

## 참고
https://github.com/yoo-jaein/TIL/blob/main/JPA/Query_method_or_JPQL_or_Querydsl.md  

