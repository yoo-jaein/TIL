# JWT

## JSON Web Token
JSON Web Token은 두 개체 간에 안전하게 클레임을 나타내기 위한 개방형 산업 표준 RFC 7519 방법이다. 토큰은 개인 비밀 또는 공개/개인 키를 사용하여 서명된다.

## 특징
1. 간결함(Compact)  
   JWT은 사이즈가 작기 때문에 ```URL```, ```HTTP POST 파라미터```, ```HTTP header``` 등에 포함될 수 있다. 또한, 사이즈가 작기 때문에 전송 속도가 빠르다.
2. 자체 포함(Self-contained)  
   유저에 대해 요구하는 모든 정보들이 JWT의 ```Payload```에 포함되기 때문에 여러 차례 데이터를 주고 받을 필요가 없다.

## 용도
1. 권한 부여(Authorization)  
   JWT를 사용하는 가장 일반적인 시나리오다. 사용자가 로그인하면 각 후속 요청에는 JWT가 포함되어 사용자가 해당 토큰으로 허용되는 경로, 서비스 및 리소스에 액세스할 수 있다. 싱글 사인온(Single Sign On)은 오버헤드가 적고 여러 도메인에서 쉽게 사용할 수 있기 때문에 오늘날 JWT를 널리 사용하는 기능이다.

2. 정보 교환(Information Exchange)  
   JWT는 당사자 간에 정보를 안전하게 전송하는 좋은 방법이다. 예를 들어 공개/개인 키 쌍을 사용하여 JWT에 서명할 수 있기 때문에 발신자가 누구인지 확인할 수 있다. 또한 ```Header```와 ```Payload```를 사용하여 서명을 계산하므로 콘텐츠가 변조되지 않았는지 확인할 수도 있다.

## 구조
JWT는 다음의 세 파트로 나뉘어진다.

- ```Header```
- ```Payload```
- ```Signature```

세 파트는 각각 점(.)으로 연결되어 일반적으로 JWT는 다음의 메시지 형식을 가진다.

```
xxxxxx.yyyyyy.zzzzzz
```

### Header
JWT의 첫 번째 파트인 Header는 일반적으로 ```토큰 유형```(JWT), ```서명 알고리즘```(HMAC/SHA256/RSA)의 두 부분으로 구성된다.

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

위의 JSON은 Base64Url로 인코딩된 JWT Header다.

### Payload
JWT의 두 번째 파트는 ```Claim```을 포함하는 ```Payload```다. ```Claim```은 사용자와 데이터에 대한 요청(설명)을 말한다. ```Claim```에는 등록된 클레임(```Registered claims```), 공개 클레임(```Public claims```), 비공개 클레임(```Private claims```)의 세 가지 유형이 있다.

- ```Registered claims``` : 미리 정의된 claim이다. 필수는 아니지만 사용 권장된다. iss(issue, 발급자), exp(expiration time, 만료 시간), sub(subject, 제목), aud(audience, 청중) 등이 있다.
- ```Public claims``` : JWT 사용자끼리 자유롭게 정의할 수 있다. 충돌을 방지하려면 IANA JWT 레지스트리의 claim이나, 충돌 방지 네임스페이스를 포함한 URI로 정의해야 한다.
- ```Private claims``` : ```Registered claims```나 ```Public claims```가 아닌 당사자 간에 정보를 공유하기 위해 생성된 커스텀 claim이다.

> 참고!  
> JWT는 간결해야 하기 때문에 **```Claim``` 이름은 3개의 characters 길이를 가져야 한다.**

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```

위의 JSON은 Base64Ur로 인코딩된 JWT Payload다.

> 참고!  
> **서명된 토큰의 경우 이 정보는 변조로부터 보호되지만 통신 과정에서 누구나 읽을 수 있다.** 암호화되지 않은 경우 JWT의 Payload 또는 Header 요소에 비밀 정보를 넣어서는 안 된다.  

### Signature
JWT의 세 번째 파트는 ```Signature```다. 서명을 하려면 ```인코딩된 Header```, ```인코딩된 Payload```, ```암호```, ```Header에 명시된 알고리즘```이 필요하다. 

예를 들어 HMAC SHA256 알고리즘을 사용하려는 경우 ```Signature```는 다음의 방식으로 생성된다.

```
HMACSHA256(
   base64UrlEncode(header) + "." +
   base64UrlEncode(payload),
   secret
)
```

서명은 메시지가 도중에 변조되지 않았는지 확인하는 데 사용된다. 개인 키로 서명된 토큰의 경우 JWT의 보낸 사람이 누구인지 확인할 수도 있다.  

## 메시지
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

이렇게 만들어진 JWT는 HTML 및 HTTP 환경에서 쉽게 전달할 수 있게 구분된 3개의 Base64URL 문자열 포맷을 가진다. SAML과 같은 XML 기반 표준과 비교했을때 훨씬 간결하다.

## jwt.io
https://jwt.io/  
jwt.io 웹 사이트의 디버거를 사용하여 JWT를 디코딩, 확인 및 생성할 수 있다.  

## 참고
https://jwt.io/introduction  
https://ko.wikipedia.org/wiki/JSON_%EC%9B%B9_%ED%86%A0%ED%81%B0  
https://auth0.com/learn/json-web-tokens/  