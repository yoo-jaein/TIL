# QueryBuilder

## ExistsQueryBuilder
필드가 존재하는 문서

```java
ExistsQueryBuilder existsQueryBuilder = QueryBuilders.existsQuery("price");
```

## IdsQueryBuilder
특정 id에 해당하는 문서

```java
String productId = "1122334455";

IdsQueryBuilder idsQueryBuilder = QueryBuilders.idsQuery().addIds(productId);
```

## BoolQueryBuilder
다른 쿼리들의 불리언 조합에 매치되는 문서

```java
String name = "iPhone12";

BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery()
    .must(QueryBuilders.matchQuery("name", name))
    .must(QueryBuilders.termQuery("isVisible", true));
```
## MatchAllQueryBuilder

## MatchQueryBuilder
```java
String name = "iPhone12";

MatchQueryBuilder matchQueryBuilder = new MatchQueryBuilder("name", name);
```

## MultiMatchQueryBuilder

## RangeQueryBuilder

```java
double minPrice = 5000;
double maxPrice = 9900;

RangeQueryBuilder rangeQueryBuilder = QueryBuilders.rangeQuery("price")
    .from(minPrice)
    .to(maxPrice);
```

```java
RangeQueryBuilder rangeQueryBuilder = QueryBuilders.rangeQuery("date")
    .gte(dateFrom)
    .lte(dateTo);
```

## TermQueryBuilder

```java
String name = "iPhone12";

TermQueryBuilder termQueryBuilder = new TermQueryBuilder("name", name);
```

## WildcardQueryBuilder
와일드카드

```java
String name = "iPhone";

WildcardQueryBuilder wildcardQueryBuilder = QueryBuilders.wildcardQuery("name", name + "*");
```

## PrefixQueryBuilder

```java
String prefix = "i";

PrefixQueryBuilder prefixQueryBuilder = QueryBuilders.prefixQuery("name", prefix)
    .caseInsensitive(true);
```

## 참고
https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/java-rest-high-query-builders.html    
