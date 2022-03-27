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

## 요청 메시지와 응답 메시지
```text
GET /test/hi-there.txt HTTP/1.0
Accept: text/*
Accept-Language: en,fr
```
```text
HTTP/1.0 200 OK
Content-Type: text/plain
Content-length: 19
Hi! I'm a message
```
HTTP 메시지는 단순한 문자열이다. 일반 텍스트이기 때문에 사람이 읽기 쉬우며 디버그하기도 좋다. 웹 클라이언트에서 웹 서버로 보낸 HTTP 메시지를 요청 메시지라고 부른다. 서버에서 클라이언트로 가는 메시지는 응답 메시지라고 부른다. HTTP 메시지는 다음의 세 부분으로 이루어진다.

1. 시작줄(Start-line) : 요청이라면 무엇을 해야 하는지, 응답이라면 무슨 일이 일어났는지 나타낸다.
2. 헤더(Headers) : 시작줄 다음에는 0개 이상의 헤더 필드가 이어진다. 각 헤더 필드는 쌍점(:)으로 구분되어 있는 하나의 이름과 하나의 값으로 구성된다. 헤더는 빈 줄로 끝난다.
3. 본문, 바디(Body) : 빈 줄 다음에는 이미지, 동영상, 오디오, 텍스트 등 어떤 종류의 데이터든 들어가는 메시지 바디가 있을 수 있다. 요청 메시지의 바디는 웹 서버로 데이터를 전송하는데 사용되고, 응답 메시지의 바디는 클라이언트로 데이터를 반환하는데 사용된다.

## HTTP 버전
### HTTP/0.9
HTTP의 프로토타입이다. 버전 번호도 없었다. HTTP/0.9는 이후에 차후 버전과 구별하기 위해 0.9로 불리게 됐다. 

#### 요청 메시지
```http request
GET /mypage.html
```
요청은 단일 라인으로 구성된다.

#### 응답 메시지
```http response
<HTML>
A very simple HTML page
</HTML>
```
응답도 단순하다. 오로지 파일 내용 자체로 구성된다. 0.9에는 HTTP 헤더도 없었는데, 이는 초기 HTTP는 HTML 파일만 전송될 수 있었음을 의미한다. 

### HTTP/1.0
HTTP/0.9는 매우 제한적이었다. 그래서 1.0에서는 브라우저와 서버가 유연하게 통신할 수 있도록 확장되었다.

- HTTP 버전 정보가 요청 메시지에 포함되기 시작했다.
- 상태 코드도 응답 메시지에 포함되었다. 상태 코드를 통해 브라우저가 요청에 대한 성공 여부를 알 수 있게 되었고, 그 결과에 대한 동작(특정 방법으로 그것의 로컬 캐시를 갱신하거나 사용하는 것과 같은)을 할 수 있게 되었다.
- HTTP 헤더가 요청과 응답에 도입되었다.

#### 요청 메시지
```http request
GET /mypage.html HTTP/1.0
User-Agent: NCSA_Mosaic/2.0 (Windows 3.1)

```

#### 응답 메시지
```http response
200 OK
Date: Tue, 15 Nov 1994 08:12:31 GMT
Server: CERN/3.0 libwww/2.17
Content-Type: text/html
<HTML>
A page with an image
  <IMG SRC="/myimage.gif">
</HTML>
```

### HTTP/1.1
HTTP의 첫번째 표준 버전인 HTTP/1.1은 HTTP/1.0이 나온지 몇 달 안되서 1997년 초에 공개되었다.

- 커넥션이 재사용될 수 있게 했다. 
- 파이프라이닝을 추가하여 첫번째 요청에 대한 응답이 완전히 전송되기 이전에 두번째 요청 전송을 가능케 하여 커뮤니케이션의 레이턴시를 낮췄다.
- 청크된 응답을 지원한다.
- 추가적인 캐시 제어 메커니즘이 도입되었다.
- 언어, 인코딩, 미디어 타입을 포함한 컨텐츠 협상이 도입되었다.

#### 요청 메시지
```http request
GET /static/img/header-background.png HTTP/1.1
Host: developer.mozilla.org
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9; rv:50.0) Gecko/20100101 Firefox/50.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://developer.mozilla.org/en-US/docs/Glossary/Simple_header

```

#### 응답 메시지
```http response
200 OK
Age: 9578461
Cache-Control: public, max-age=315360000
Connection: keep-alive
Content-Length: 3077
Content-Type: image/png
Date: Thu, 31 Mar 2016 13:34:46 GMT
Last-Modified: Wed, 21 Oct 2015 18:27:50 GMT
Server: Apache

(image content of 3077 bytes)
```

### HTTP/2.0
HTTP/2.0은 HTTP/1.1 버전과 다른 몇가지 근본적인 차이점을 가지고 있다.

- 텍스트 프로토콜이라기 보다는 이진 프로토콜이다. 더 이상 사람이 읽을 수 없는 형태다.
- 스트림을 이용해서 한 번의 커넥션으로 동시에 여러 개의 데이터를 병렬로 주고 받을 수 있다.
- 연속된 요청 사이에서 매우 유사한 내용으로 존재하는 헤더들을 압축시킬 수 있다.
- 요청이 없어도 서버가 클라이언트에게 보낼 수 있는 서버 푸쉬에 의해 클라이언트의 요청을 최소화할 수 있다.

## 참고
HTTP 완벽 가이드 1장  
https://developer.mozilla.org/ko/docs/Web/HTTP/Basics_of_HTTP/Evolution_of_HTTP  