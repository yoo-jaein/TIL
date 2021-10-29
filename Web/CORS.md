# CORS
Cross-Origin Resource Sharing, ```CORS```란 다른 도메인의 자원 호출을 승인하거나 차단하는 것을 결정하는 것이다.

## 등장 배경
HTTP Request는 기본적으로 다른 도메인의 

- \<img> 이미지
- \<link rel="stylesheet" href="..."> CSS
- JavaScript 라이브러리
- \<video> 비디오
- \<audio> 오디오 

등을 가져올 수 있다. 이렇게 **다른 도메인의 자원을 가져오는 요청을 ```Cross-Site HTTP Request```라 한다.**    

그러나 **```<script></script>``` 내의 JavaScript 스크립트에서 생성된 HTTP Request는** ```SOP(Same-Origin Policy)```를 적용받기 때문에 **Cross-Site HTTP Request에 해당하지 않는다.** ```SOP```는 다른 도메인의 자원과 상호작용하는 것을 제한하는 보안 정책이다.  

**```CORS```는 이렇게 스크립트에서 생성되는 HTTP Request에 대해 ```SOP```를 우회하여 다른 도메인의 자원과 상호작용할 수 있도록 만드는 설정이다.**  

## CORS
CORS와 관련된 Request의 종류에는 여러 조합이 있다.

### Simple Request
```단순 요청(Simple Request)```은 CORS ```Preflight``` 없이 즉시 요청된다. ```Simple Request```의 조건은 다음과 같다.

- ```GET```, ```HEAD```, ```POST``` 메서드 중 하나를 사용
- User-Agent가 자동으로 설정한 헤더를 제외하고 아래의 목록의 헤더를 가질 수 있음
  - ```Accept```
  - ```Accept-Language```
  - ```Content-Language```
  - ```Content-Type```
- 만약 Content-Type 헤더를 가진다면 아래의 목록 중 하나의 값을 가져야 함
  - ```application/x-www-form-urlencoded```
  - ```multipart/form-data```
  - ```text-plain```
- 커스텀 헤더를 사용할 수 없음

### Preflight Request
Simple Request가 아닌 일부 HTTP Request의 경우, 브라우저가 서버에게 ```Preflight Request```를 보낸다. **실제 요청을 보내기 전, 먼저 ```OPTIONS``` 메서드를 사용해서 다른 출처의 리소스에 ```Preflight Request```를 전송하고 실제 요청을 보내는 것이 안전한지 확인한다.** ```OPTIONS``` 메서드는 통신 옵션을 확인할 수 있기 때문에 **```Preflight Request```를 통해 해당 리소스가 어떤 ```method```, ```header```, ```content-type```을 지원하는지 알 수 있다.** Cross-Site access는 사용자의 데이터에 영향을 미칠 수 있기 때문에 실제 비행 전에(preflight) **안전성을 한 번 체크하는 것이다.**  

```Preflight Request```를 거쳐야 하는 HTTP Request의 조건은 다음과 같다.

- 메서드 제한 없음
- Content-Type의 값 제한 없음 (예 : json)
- 커스텀 헤더 제한 없음

서버가 Preflight Request에 승인한다는 응답이 돌아오면 브라우저가 실제 요청(actual request)을 보낸다.  

Preflight Request는 일반적인 상황에서는 브라우저에서 자동으로 발생되고 처리된다. 따라서 **개발자가 이 요청을 직접 작성할 필요가 없다.**  

### Credentials
**기본적으로 Cross-Site HTTP Request에는 쿠키와 인증 정보를 보내지 않는다.** 따라서 HTTP 쿠키와 HTTP Authentication 정보를 활용하려면 Credentials를 true로 설정해야 한다. HTTP 헤더 중 ```Access-Control-Allow-Credentials``` : ```true```로 설정한다. 

### Wildcards
헤더 값에 와일드카드("*")를 설정할 수 있다. 그러나 **Credentials 설정을 true로 지정한 경우(쿠키나 인증 정보를 사용하는 경우), 와일드카드를 사용해서는 안 되고 명시적으로 출처를 지정해야만 한다.** Credential 설정을 따로 하지 않았다면(default: false) 와일드카드를 사용해도 된다.

```
Access-Control-Allow-Credentials : true
Access-Control-Allow-Origin : "*" (X)
```

```
Access-Control-Allow-Credentials : true
Access-Control-Allow-Origin : "http://localhost:8080" (O)
```

```
Access-Control-Allow-Credentials : true
Access-Control-Allow-Headers : "*" (X)
```

```
Access-Control-Allow-Credentials : true
Access-Control-Allow-Headers : X-PINGOTHER, Content-Type (O)
```

```
Access-Control-Allow-Credentials : true
Access-Control-Allow-Methods : "*" (X)
```

```
Access-Control-Allow-Credentials : true
Access-Control-Allow-Methods : POST, GET (O)
```


## CORS 설정
```CORS```를 사용해 Cross-Origin access를 허용할 수 있다. **기본적으로 Node.js, Spring Boot 등의 서버단에서 ```CORS``` 설정을 해줘야 한다.**  

## 참고
https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS  
https://developer.mozilla.org/ko/docs/Web/Security/Same-origin_policy  
https://homoefficio.github.io/2015/07/21/Cross-Origin-Resource-Sharing/  
