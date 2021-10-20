# Using Elasticsearch with Spring Boot
스프링 부트 프로젝트에서 일래스틱서치 쓰기

## Elasticsearch란
[Elasticsearch.md](https://github.com/yoo-jaein/TIL/blob/main/Elasticsearch/Elasticsearch.md) 참고  

## Spring Data Elasticsearch
Spring Data Elasticsearch는 단일 Elasticsearch(이하 ES) 노드 또는 클러스터에 연결된 ES 클라이언트에서 작동한다. ES 클라이언트를 사용해서 클러스터에서 작업을 할 수 있지만, Spring Data ES를 사용하는 애플리케이션들은 일반적으로 ES Operation이나 ES Repository를 사용한다. 

## 1단계 : Elasticsearch 인스턴스 생성하기
생략

## 2단계 : Elasticsearch 의존성 추가하기
사용하는 ES 버전에 따라 Spring Data ES의 버전이 다르다. [Spring Data ES 공식 문서](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#preface.versions) 참고

### pom.xml
```xml
<dependency>
	<groupId>org.springframework.data</groupId>
	<artifactId>spring-data-elasticsearch</artifactId>
</dependency>
```

### build.gradle
```groovy
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-data-elasticsearch'
}
```

## 3단계 : 클라이언트 선택하기
### Transport Client
TransportClient는 ES 7에서 deprecated되었고 ES 8에서 제거될 예정이다. 기존 버전을 사용하는 ES를 위해 지원되고 있다. Spring Data ES에서는 TransportClient 대신 High Level REST Client를 사용할 것을 강력하게 권고하고 있다.

### High Level REST Client
Java High Level REST Client(JHLC)는 ES의 기본 클라이언트로, 기존에 사용되던 TransportClient와 동일한 요청/응답 오브젝트를 가지는 대체재이다. Asynchronous(비동기) 호출들은 스레드 풀에서 작동하며 요청이 완료되면 콜백을 알려야 한다. 이 글에서는 Spring Data ES가 JHLC(```RestHighLevelClient```)를 이용하도록 구성하여 ES와 연결할 예정이다. 

### Reactive Client
```ReactiveElasticsearchClient```는 WebClient를 기반으로 하는 비공식 드라이버이다. 이를 통한 호출들은 스레드 풀 범위의 유형으로 래핑되지 않고 reactive stack에 직접적으로 작동된다. 
 
## 4단계 : 환경 설정하기
```java
@Configuration
@EnableElasticsearchRepositories(basePackages = "com.demo.elasticsearch.repository")
@ComponentScan(basePackages = {"com.demo.elasticsearch"})
public class ElasticsearchClientConfig extends AbstractElasticsearchConfiguration {
	@Override
	@Bean
	public RestHighLevelClient client() {
		final ClientConfiguration clientConfiguration = 
			ClientConfiguration
				.builder()
				.connectedTo("localhost:9200")
				.build();
		
		return RestClients
			.create(clientConfiguration)
			.rest();
	}
}
```

위 코드를 통해 스프링 부트 프로젝트를 ES 인스턴스에 연결했다. ```ClientConfiguration``` 객체를 통해 withProxy(), withPathPrefix(), withConnectTimeout(), withSocketTimeout(), withDefaultHeaders(), withBasicAuth(), withHeaders() 등 다양한 옵션을 설정할 수 있다. url, port 등의 정보는 별도의 yml 파일로 분리하여 구성할 수도 있다.

## 5단계 : Elasticsearch 객체 매핑하기
Spring Data ES의 객체 매핑은 ES에 저장된 JSON 표현과 자바 객체(도메인 엔티티)를 연결하는 작업이다. 기존의 Spring Data ES에서는 Jackson을 이용하여 매핑했지만, 3.2.x부터 Meta Model Object Mapping 방법이 도입되어 사용되고 있다.  

Meta Model Object Mapping은 MappingElasticsearchConverter를 이용한다. 이는 어노테이션 기반으로 작동하며 오브젝트를 ES의 document에 매핑한다. 아래 어노테이션들을 이용한다.

- ```@Document``` : 이 클래스가 데이터베이스에 매핑할 클래스임을 나타낸다. 
- ```@Id``` : 식별 목적으로 사용되는 필드를 나타낸다.
- ```@Transient``` : 기본적으로 모든 필드는 document에 매핑되는데 이 어노테이션이 적용되면 그 필드는 제외한다.
- ```@Field``` : ES에 매핑될 필드를 나타낸다.

```java
@Document(indexName = "productindex")
public class Product {
	@Id
	private String id;

	@Field(type = FieldType.Text, name = "name")
	private String name;

	@Field(type = FieldType.Double, name = "price")
	private Double price;

	@Field(type = FieldType.Keyword, name = "category")
	private String category;
}
```

```@Document```는 ES index 이름을 명시한다. ```@Id```는 index의 고유 식별자인 id 필드와 매핑시킨다. 512 characters의 제약 조건이 있다. ```@Field```는 필드 유형을 구성하고, ES의 필드 이름과 다르게 설정할 수도 있다. 이러한 어노테이션을 기반으로 ES에서 productindex라는 이름으로 index가 생성된다. 

## 6단계 : Repository 생성하기
Spring Data JPA에서 JpaRepository를 사용하던 방식과 유사하게 Spring Data ES에서는 ```ElasticsearchRepository```를 이용한다. 아래의 ProductRepository는 ```ElasticsearchRepository```를 통해 save(), saveAll(), find(), findAll() 등의 메서드를 이용할 수 있다.

```java
public interface ProductRepository
	extends ElasticsearchRepository<Product, String> { }
```

JpaRepository 방식과 동일하게 [메서드 이름의 키워드](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#elasticsearch.query-methods.criterions)를 통해 쿼리를 생성할 수 있다.  

```java
interface BookRepository extends Repository<Book, String> {
	List<Book> findByNameAndPrice(String name, Integer price);
}
```

또한, ```@Query``` 어노테이션을 사용해 쿼리를 명시할 수도 있다. 이 때 어노테이션에 들어가는 쿼리는 유효한 ES JSON 쿼리여야 한다.  

```java
interface BookRepository extends ElasticsearchRepository<Book, String> {
	@Query("{\"match\": {\"name\": {\"query\": \"?0\"}}}")
	Page<Book> findByName(String name,Pageable pageable);
}
```

## 7단계 : ElasticsearchRestTemplate 활용하기
Spring Data의 Repository는 복잡한 쿼리가 필요할 때 적합하지 않다. 이런 상황에는 ```ElasticsearchRestTemplate```을 사용한다. ```ElasticsearchRestTemplate```은 High Level REST Client를 사용해 ES index에 대해 호출할 수 있는 연산을 모아둔 ```ElasticsearchOperations``` 인터페이스를 구현한 것이다.  

```java
@Configuration
@EnableElasticsearchRepositories(basePackages = "com.demo.elasticsearch.repository")
@ComponentScan(basePackages = {"com.demo.elasticsearch"})
public class ElasticsearchClientConfig extends AbstractElasticsearchConfiguration {
	@Override
	@Bean
	public RestHighLevelClient client() {
		final ClientConfiguration clientConfiguration = 
			ClientConfiguration
				.builder()
				.connectedTo("localhost:9200")
				.build();

		return RestClients
			.create(clientConfiguration)
			.rest();
	}
}
```

베이스 클래스인 ```AbstractElasticsearchConfiguration```가 이미 ```ElasticsearchRestTemplate``` 빈을 제공하기 때문에 추가적인 설정이 없다.

### Indexing 
```java
@Service
public class ProductSearchService {
	private static final String PRODUCT_INDEX = "productindex";
	
	private ElasticsearchOperations elasticsearchOperations;
	
	@Autowired
	public ProductSearchService(final ElasticsearchOperations elasticsearchOperations) {
		super();
		this.elasticsearchOperations = elasticsearchOperations;
	}
	
	public List<String> createProductIndexBulk(final List<Product> products) {
		List<IndexQuery> queryList = products.stream()
			.map(product->
			new IndexQueryBuilder()
				.withId(product.getId().toString())
				.withObject(product).build())
			.collect(Collectors.toList());
		
		return elasticsearchOperations
			.bulkIndex(queryList, IndexCoordinates.of(PRODUCT_INDEX));
	}
}
```

productindex index에 여러 Product를 한 번에 추가하기 위해 ElasticsearchOperations의 bulkIndex() 메서드를 사용했다. 하나의 Product를 추가하려면 index() 메서드를 이용하면 된다.

### Searching
index에서 document를 검색하기 위한 메서드들이 있다. 이런 메서드는 쿼리를 나타내는 ```Query``` 파라미터를 사용한다. ```Query```는 인터페이스이고 Spring Data ES는 ```CriteriaQuery```, ```StringQuery```, ```NativeSearchQuery```의 [세 가지 구현](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#elasticsearch.operations.queries)을 제공한다.

#### CriteriaQuery
```java
Criteria criteria = new Criteria("price").greaterThan(42.0).lessThan(34.0);
Query query = new CriteriaQuery(criteria);
```

#### StringQuery
```java
Query query = new SearchQuery("{ \"match\": { \"firstname\": { \"query\": \"Jack\" } } } ");
SearchHits<Person> searchHits = operations.search(query, Person.class);
```

#### NativeSearchQuery
```java
Query query = new NativeSearchQueryBuilder()
    .addAggregation(terms("lastnames").field("lastname").size(10)) //
    .withQuery(QueryBuilders.matchQuery("firstname", firstName))
    .build();

SearchHits<Person> searchHits = operations.search(query, Person.class);
```

## 참고
https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#reference  
https://reflectoring.io/spring-boot-elasticsearch/  
