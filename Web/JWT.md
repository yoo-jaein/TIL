# JWT

## JSON Web Token
JSON Web Token은 두 개체 간에 안전하게 클레임을 나타내기 위한 개방형 산업 표준 RFC 7519 방법이다. 토큰은 개인 비밀 또는 공개/개인 키를 사용하여 서명된다.

## 용도
1. 권한 부여(Authorization)
   JWT를 사용하는 가장 일반적인 시나리오다. 사용자가 로그인하면 각 후속 요청에는 JWT가 포함되어 사용자가 해당 토큰으로 허용되는 경로, 서비스 및 리소스에 액세스할 수 있다. 싱글 사인온(Single Sign On)은 오버헤드가 적고 여러 도메인에서 쉽게 사용할 수 있기 때문에 오늘날 JWT를 널리 사용하는 기능이다.

2. 정보 교환(Information Exchange)
   JWT는 당사자 간에 정보를 안전하게 전송하는 좋은 방법이다. 예를 들어 공개/개인 키 쌍을 사용하여 JWT에 서명할 수 있기 때문에 발신자가 누구인지 확인할 수 있다. 또한 헤더(header)와 페이로드(payload)를 사용하여 서명을 계산하므로 콘텐츠가 변조되지 않았는지 확인할 수도 있다.

## 구조
JWT는 다음의 세 파트로 나뉘어진다.

- Header
- Payload
- Signature

세 파트는 각각 점(.)으로 연결되어 일반적으로 JWT는 다음의 메시지 형식을 가진다.

```
xxxxxx.yyyyyy.zzzzzz
```

## jwt.io
https://jwt.io/

## 참고
https://jwt.io/introduction  
https://ko.wikipedia.org/wiki/JSON_%EC%9B%B9_%ED%86%A0%ED%81%B0  