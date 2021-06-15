# Entity and DTO

## DTO 
Data Transfer Object  
계층간 데이터 교환을 위한 객체(Java Beans)   

DB에서 데이터를 얻어 Service나  Controller등으로 보낼 때 사용하는 객체이다. 로직을 갖고 있지 않은 순수한 데이터 객체이며, getter/setter 메서드만을 갖는다. 


## Entity
Domain model  

실제로 데이터베이스 물리 테이블과 1:1 매핑되어 바인딩 되어있다. 

## Entity와 DTO를 분리하는 이유

- View Layer와 DB Layer 역할을 철저하게 분리하기 위해.  
  - 테이블과 매핑되는 Entity 클래스가 변경되면 여러 클래스에 영향을 끼치게 된다. View Layer와 통신하는 DTO 클래스(Request/Response 클래스)는 자주 변경되므로 분리하는 것이 좋다. 비즈니스 로직에서 실시간으로 엔티티 변경이 필요한 경우에만 Entity 조회를 사용하도록 한다.

## DTO를 반드시 사용해야 하는 이유

1. 엔티티와 API가 일대일 대응의 관계를 가진다면 엔티티에 수정이 일어날 때마다 API 스펙을 일일히 변경해줘야한다. 이는 매우 번거로운 작업이며 컴파일 에러로 이를 감지할 수 없기 때문에 에러 원인을 찾기가 어렵다. DTO를 사용하면 DTO -> 엔티티 과정에서 컴파일 에러가 발생되므로 엔티티의 변경사항을 반드시 파악할 수 있다.  
   

2. 하나의 엔티티에 대해 여러 개의 API가 존재할 수 있다. 각각의 API가 요구하는 엔티티에 대한 데이터는 모두 다를 확률이 높다. 엔티티 하나만으로는 모든 API에 대한 요구사항을 한 번에 만족시킬 수 없다. 


3. 엔티티를 그대로 넘겨줄 경우, 엔티티가 가진 정보 외의 것들은 넘겨주지 못한다. DTO를 사용하면 엔티티의 정보 외에 추가적으로 필요한 정보도 함께 넘겨줄 수 있다.  


4. 엔티티에서 비밀번호와 같이 감춰야할 정보들도 모두 View Layer로 전달될 수 있다. 이러한 컬럼에 @JsonIgnore를 사용해 전달을 막을 수 있지만, 엔티티가 API 스펙에 의존성을 갖게 되므로 좋지 않다.   

   
## 참고
https://woowacourse.github.io/javable/post/2020-08-31-dto-vs-entity/  
https://stackoverflow.com/questions/36174516/rest-api-dtos-or-not  
https://github.com/HomoEfficio/dev-tips/blob/master/Entity-%EB%8C%80%EC%8B%A0-DTO%EB%A1%9C-%EB%B0%98%ED%99%98.md  