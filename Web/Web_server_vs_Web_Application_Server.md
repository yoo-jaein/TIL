# Web server vs Web Application Server
웹서버와 WAS의 차이점

## Web server
- 클라이언트에게 HTTP Request를 받아서 서비스하는 역할
- Static Page(Image, HTML, CSS, JS와 같은 자원)를 제공
- 항상 동일한 자원을 반환
- Web Server는 파일의 경로를 요청으로 받아 일치하는 파일을 반환한다.
- Dynamic Page를 제공하기 위한 요청을 WAS로 전달하고 WAS로부터 Response를 받아 클라이언트에게 전달되어 웹 브라우저로 제공
- HTTPS를 지원
- 웹서버 메모리 캐시
- 종류: nginx, Apache Web Server

## WAS
- DB조회나 각종 로직 처리가 수반되는 요청을 처리하여 표출되는 동적인 페이지를 서비스하기 위한 Application Server
- HTTP를 통해 컴퓨터의 각종 어플리케이션을 수행해주는 미들웨어
- Web Server의 기능 + DB 접속, 프로그램 실행 환경, 비즈니스 로직 처리, 트랜잭션 관리
- 종류: Tomcat, JBoss

## 왜 웹서버와 WAS를 구분할까?
클라이언트가 특정 페이지를 요청하면 먼저 HTML을 받아오고 HTML에서 표현하고자 하는 이미지등을 다시 요청하는 과정을 거친다.
Web Server를 통해 정적인 컨텐츠를 서비스하도록 하면 이미지와 같은 것들은 Application Server까지 가지 않고 Web Server에서 서비스하게 되어 부하 분산 효과를 가져올 수 있다.
Web Server만으로 요청을 처리하려면 사용자의 요청에 대한 모든 응답결과를 미리 만들어 둬야 한다.
WAS만으로 요청을 처리하면 WAS는 DB에 접속한다던지, 각종 로직처리로 바쁜데 정적인 컨텐츠에 대한 요청까지 처리하려면 부하가 많이 걸릴 수 있다.
WAS는 DB에 붙어서 처리하거나 시스템의 자원에 접근해서 처리해야하는 동적인 페이지 서비스를 담당하게 하고 정적인 컨텐츠에 대해서는 Web Server에서 처리하는 방식으로 부하분산을 유도한다.
SSL에 대한 암복호화 처리 같은 것은 Web Server에서 담당하도록 처리

## 참고
https://taes-k.github.io/2019/05/24/webserver/  