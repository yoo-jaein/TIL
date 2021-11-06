# QueryBuilder

## ExistsQueryBuilder
A filter to filter only documents where a field exists in them.

## IdsQueryBuilder
Constructs a query that will match only specific ids within all types.

```java
String productId = "1122334455";
IdsQueryBuilder idsQueryBuilder = QueryBuilders.idsQuery().addIds(productId);
 
NativeSearchQuery nativeSearchQuery = new NativeSearchQueryBuilder()
    .withQuery(idsQueryBuilder)
    .build();
 
SearchHit<Product> productSearchHit = elasticsearchOperations.searchOne(nativeSearchQuery, Product.class);
```

## BoolQueryBuilder
A Query that matches documents matching boolean combinations of other queries.

```java
String name = "iPhone12";

BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery()
    .must(QueryBuilders.matchQuery("name", name))
    .must(QueryBuilders.termQuery("isVisible", true));
 
NativeSearchQuery nativeSearchQuery = new NativeSearchQueryBuilder()
    .withQuery(boolQueryBuilder)
    .build();

long count =  elasticsearchOperations.count(nativeSearchQuery, Product.class);
```
## MatchAllQueryBuilder
A query that matches on all documents.

## MatchQueryBuilder
Creates a match query with type "BOOLEAN" for the provided field name and text.

## MultiMatchQueryBuilder
Creates a match query with type "BOOLEAN" for the provided field name and text.

## RangeQueryBuilder
A Query that matches documents within an range of terms.

## RegexQueryBuilder
A Query that matches documents containing terms with a specified regular expression.

## TermQueryBuilder
A Query that matches documents containing a term.

## WildcardQueryBuilder
Implements the wildcard search query.

## PrefixQueryBuilder

## DisMaxQueryBuilder

## 참고
https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/java-rest-high-query-builders.html    
