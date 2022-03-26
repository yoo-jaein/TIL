# HTTP
HTTP는 전 세계 웹 브라우저, 서버, 웹 애플리케이션의 공용어다. 

## 웹 클라이언트와 서버
웹에서 사용하는 다양한 콘텐츠는 웹 서버에 존재한다. 클라이언트는 이 서버에게 /index.html을 달라는 HTTP 요청을 보내고 서버는 요청된 데이터를 찾고 성공했다면 그것의 타입, 길이 등의 정보와 함께 HTTP 응답에 실어서 전달한다. 웹 서버가 가지고 있는 리소스에는 HTML, CSS, 이미지, 동영상 등의 정적 리소스도 있고 요청에 따라 달라지는 동적인 리소스도 있다. HTTP는 다양한 데이터 타입을 다루기 때문에 MIME(Multipurpose Internet Mail Extensions) 타입이라는 데이터 포맷 라벨을 사용한다. 웹 서버는 모든 HTTP 객체 데이터에 MIME 타입을 붙인다. 웹 브라우저는 서버로부터 객체를 돌려받을 때, 다룰 수 있는 객체인지 MIME 타입을 통해 확인한다. 다음은 자주 사용되는 MIME 타입이다.

- HTML 문서 : text/html
- plain ASCII 텍스트 문서 : text/plain
- JPEG 이미지 : image/jpeg
- GIF 이미지 : image/gif
- 파워포인트 프레젠테이션 : application/vnd.ms-powerpoint

## URI, URL, URN
웹 서버 리소스는 자신만의 이름을 가지고 있다. 리소스의 이름은 URI(Uniform Resource Identifier)라고 불린다. URI에는 URL과 URN이라는 것이 있다.

```text
[URL] http://www.joes-hardware.com/specials/saw-blade.gif
```
URL(Uniform Resource Locator)은 프로토콜과 특정 서버, 리소스의 위치를 서술한다. 위 URL은 http 프로토콜을 사용해서 www.joes-hardware.com으로 이동한 뒤 /specials/saw-blade.gif라는 이름의 리소스를 가져오라는 의미다. 프로토콜이 명시된 부분을 스킴(scheme)이라고도 부른다. 일반적으로 말하는 URI가 URL이다.

```text
[URN] urn:ietf:rfc:2141
```
URN(Uniform Resource Name)은 한 리소스에 대해 변하지 않는 이름을 말한다. 위치에 독립적인 것이 특징이다. 위 URN은 인터넷 표준 문서 'RFC 2141'이 어디에 있든 상관없이 그것을 지칭하기 위해 사용할 수 있다.

## HTTP의 트랜잭션(요청과 응답)
HTTP의 트랜잭션은 클라이언트가 서버로 보내는 요청 명령과 서버가 클라이언트에게 돌려주는 응답 결과로 구성되어 있다. 모든 HTTP 요청 메시지는 한 개의 메서드를 갖는다. 메서드는 서버에게 어떤 동작이 취해져야 하는지를 말해준다. 다음은 자주 사용되는 메서드다.

- GET : 서버에서 클라이언트로 지정한 리소스를 보내라.
- PUT : 클라이언트에서 서버로 보낸 데이터를 지정한이름의 리소스로 저장하라.
- DELETE : 지정한 리소스를 서버에서 삭제하라.
- POST : 클라이언트 데이터를 서버 게이트웨이 애플리케이션으로 보내라.
- HEAD : 지정한 리소스에 대한 응답에서 HTTP 헤더 부분만 보내라.

모든 HTTP 응답 메시지는 상태 코드와 함께 반환된다. 각 숫자 상태 코드에 텍스트로 된 사유 구절(reason phrase)도 포함된다. 다음은 자주 사용되는 [상태 코드](https://github.com/yoo-jaein/TIL/blob/main/Web/HTTP_Status_Code.md)다.

- 200 : OK. 문서가 정상적으로 반환되었다.
- 404 : Not Found. 리소스를 찾을 수 없다.

## 참고
HTTP 완벽 가이드 1장  