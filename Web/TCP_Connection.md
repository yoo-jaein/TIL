# TCP Connection
TCP, Transmission Control Protocol, 전송 제어 프로토콜

## TCP/IP
```text
[Application Layer] HTTP
[Transport Layer] TCP
[Network Layer] IP
```
HTTP는 애플리케이션 계층의 프로토콜이며 신뢰성 있는 인터넷 전송 프로토콜인 TCP/IP를 기반하고 있다. TCP는 다음을 제공한다.
- 오류 없는 데이터 전송
- 항상 전송 순서를 보장
- 데이터 스트림
TCP/IP란 TCP와 IP가 층을 이루는 패킷 교환 네트워크 프로토콜의 집합니다. TCP/IP는 각 네트워크와 하드웨어의 특성을 숨기고, 어떤 종류의 컴퓨터나 네트워크든 서로 신뢰성 있는 의사소통을 하게 해 준다. 일단 TCP 커넥션이 맺어지면, 클라이언트와 서버 컴퓨터 간에 교환되는 메시지가 없어지거나, 손상되거나, 순서가 뒤바뀌어 수신되는 일은 결코 없다. 

## 커넥션 맺기
HTTP 클라이언트가 서버랑 통신하기 위해서, 먼저 IP(Internet Protocol, 인터넷 프로토콜) 주소와 포트번호를 사용해 클라이언트와 서버 사이에 TCP/IP 커넥션을 맺어야 한다. TCP에서는 서버 컴퓨터에 대한 IP 주소와 포트번호를 알아내려면 URL을 이용하면 된다. 이후 TCP 커넥션을 생성한다.
```text
http://207.200.83.29:80/index.html -> IP 주소: 207.200.83.29, 포트번호: 80
http://www.netscape.com:80/index.html -> IP 주소: 호스트 명(www.netscape.com)이 있으므로 DNS로 IP 주소를 변환. 포트번호: 80
http://www.netscape.com/index.html -> IP 주소: 호스트 명(위와 동일)이 있으므로 IP 주소 변환. 포트번호: URL에 포트번호가 없다면 기본값 80이라 가정
```

일반적으로 웹 브라우저가 서버로부터 HTML 문서를 받아오는 과정은 다음과 같다.
1. 웹 브라우저는 서버의 URL에서 호스트 명을 추출한다.
2. 웹 브라우저는 서버의 호스트 명을 IP로 변환한다.
3. 웹 브라우저는 URL에 포트번호가 있다면 추출한다.
4. 웹 브라우저는 웹 서버와 TCP 커넥션을 맺는다.
5. 웹 브라우저는 서버에 HTTP 요청을 보낸다.
6. 서버는 웹 브라우저에 HTTP 응답을 돌려준다.
7. 커넥션이 닫히면 웹 브라우저는 문서를 보여준다.


## 참고
HTTP 완벽 가이드 1장  