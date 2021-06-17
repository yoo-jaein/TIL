# JpaRepository and SimpleJpaRepository

## JpaRepository
org.springframework.data.jpa.repository.JpaRepository  

![image](https://postfiles.pstatic.net/MjAyMTA2MTVfMTIy/MDAxNjIzNzYwMTE3MDc3.p_RvMcSKtr49EtA0sI9YmWmZb2nWOhtHKXQjoobADj8g.PSLsDnD6HbE-NB5h0rD7My2CbmXPRnD6EmRSxBH9dcIg.PNG.y_jaein/image.png?type=w966)  

JpaRepository는 PagingAndSortingRepository를 상속하는 인터페이스이다. 영속성 컨텍스트를 flush하거나 배치 작업을 하는 등 JPA 관련 기능을 제공한다. JpaRepository가 상속하는 PagingAndSortingRepository는 CRUD 기능을 제공하는 CrudRepository를 상속하는 인터페이스이다. 따라서 JpaRepository는 기본적인 CRUD 기능과 Pagination, Sorting 기능까지 모두 가진 인터페이스이다. 

## SimpleJpaRepository
org.springframework.data.jpa.repository.support.SimpleJpaRepository    

SimpleJpaRepository는 JpaRepository의 구현체이다. delete(T entity), deleteById(ID id), findById(ID id), save(S entity), count(Example<S\> example), exist(Example<S\> example) 등의 기능을 제공한다.  

## SimpleJpaRepository 들여다보기
1. 클래스에 @Repository 적용  
   ![image](https://postfiles.pstatic.net/MjAyMTA2MTVfMTY5/MDAxNjIzNzU5OTgwMjg0.59AtHl7yYbTG84QwRGANNnmQalRnE4Hvz6vAuEX8RfAg.VVyFi6aA8O_5MVtoipt0bwDmbIJIZhTFhaScyQ7Vk_Ug.PNG.y_jaein/image.png?type=w966)  
    - @Repository 어노테이션을 적용함으로써 데이터베이스 익셉션(JPA 익셉션)을 Spring의 DataAccessException으로 번역해준다.  


2. 클래스에 @Transactional(readOnly = true) 적용  
   ![image](https://postfiles.pstatic.net/MjAyMTA2MTVfNTAg/MDAxNjIzNzYwMDI5ODk1.r2Cu9xTrYgpO59JU__E-ArnBEfy8YzdUqMMjE7_cJ0sg.q89p2d71ZTnt0Xd0rHPreB_2nE9TAHIjpQV_zQ42hbYg.PNG.y_jaein/image.png?type=w966)  
    - 클래스에 readOnly 트랜잭션이 적용된다. 이로 인해 Service layer에서 트랜잭션을 적용하지 않아도 Repository layer에서 트랜잭션을 시작하게 된다. 클래스 전체로 봤을 때 조회하는 메서드가 더 많으므로 readOnly = true 옵션이 적용된다.      


3. 일부 메서드에 @Transactional 적용  
   ![image](https://postfiles.pstatic.net/MjAyMTA2MTVfNjUg/MDAxNjIzNzYwMDE0NDUy.RkJRL26hx_rXr_sYTPxBUlQ4a6tb1NB_uKMJjfvyY6Ug.dby9AiKLtGkRBcVUGCpJsgZv0FGWZ1acxXh47CwJlusg.PNG.y_jaein/image.png?type=w966)  
    - Insert, Update, Delete의 경우 readOnly = true 옵션을 제외한다.  
   

4. save()  
   ![image](https://postfiles.pstatic.net/MjAyMTA2MTVfMTYg/MDAxNjIzNzYwMDAzNzU1.jHOTrgtX-3g9YpOG1PDbluUfOM-Bn0TVdltIaNnx4Ocg.CUJ3x_rrd90uirwbJFXtflAlRbHWWnhRrGm2jkNjx34g.PNG.y_jaein/image.png?type=w966)  
    - 저장할 엔티티가 새로운 엔티티면 persist하고, 이미 있는 엔티티면 merge한다.  
    - entityInformation.isNew()에서 새로운 객체인지 판별한다.  
    - 만약 Persistable 인터페이스를 구현한 객체를 빈으로 등록하면 위의 조건을 직접 정의할 수 있다.  
