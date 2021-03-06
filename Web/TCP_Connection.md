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

## TCP 커넥션 
컴퓨터는 항상 TCP 커넥션을 여러 개 가지고 있다. TCP 커넥션은 다음의 네 가지 값으로 식별된다.  
1. 발신지 IP 주소
2. 발신지 포트 번호
3. 수신지 IP 주소
4. 수신지 포트 번호

## TCP 커넥션 맺기
HTTP 클라이언트가 서버랑 통신하기 위해서는 먼저 서버의 IP(Internet Protocol, 인터넷 프로토콜) 주소와 포트번호를 알아내야 한다. TCP에서 서버 컴퓨터에 대한 IP 주소와 포트번호를 알아내려면 URL을 이용하면 된다. 
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

## TCP 세그먼트와 IP 패킷
HTTP가 메시지를 전송하고자 할 경우, 현재 연결되어 있는 TCP 커넥션을 통해서 메시지 데이터의 내용을 순서대로 보낸다. 그러면 TCP는 세그먼트라는 단위로 데이터 스트림을 잘게 나누고, 세그먼트를 IP 패킷에 담아서 하나의 IP 주소에서 다른 IP 주소로 인터넷을 통해 데이터를 전달한다. IP 헤더는 발신지 IP 주소, 수신지 IP 주소, IP 버전, 총 데이터그램 길이, 헤더 체크섬, 기타 플래그를 가진다. TCP 세그먼트 헤더는 발신지 포트 번호, 수신지 포트 번호, TCP 제어 플래그, 그리고 데이터의 순서와 무결성을 검사하기 위해 사용되는 숫자 값(순서 번호)을 포함한다.

## 참고
HTTP 완벽 가이드 1장  