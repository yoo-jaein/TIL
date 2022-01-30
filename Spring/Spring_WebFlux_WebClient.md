# Spring Webflux WebClient

## URI 경로
```java
// /products/2/attributes/13
webClient.get()
    .uri(uriBuilder - > uriBuilder
        .path("/products/{id}/attributes/{attributeId}")
        .build(2, 13))
    .retrieve();
```

## 리퀘스트 바디
```java
webClient.post()
    .uri("/user/" + userId)
    .bodyValue(request) // UserRequest request
    .retrieve();
```

## 쿼리 파라미터
```java
// /products/?name=AndroidPhone&color=black&deliveryDate=13/04/2019"
webClient.get()
    .uri(uriBuilder - > uriBuilder
        .path("/products/")
        .queryParam("name", "AndroidPhone")
        .queryParam("color", "black")
        .queryParam("deliveryDate", "13/04/2019")
        .build())
    .retrieve();
```

## 참고
https://docs.spring.io/spring-framework/docs/current/reference/html/web-reactive.html#webflux-client  
https://www.baeldung.com/webflux-webclient-parameters  