# Database connection pool
데이터베이스 커넥션 풀

## WAS에서 데이터베이스 서버로 접근하는 과정
웹 애플리케이션이 동작하는 WAS(Web Applicaton Server)에서 데이터베이스 서버에 접근하고 데이터를 가져오기 위해서 다음 단계를 거쳐야 한다.
1. 데이터베이스 서버 접속을 위해 JDBC 드라이버를 로드한다.
2. 데이터베이스 접속 정보와 DriverManager.getConnection() 메서드를 통해 데이터베이스 Connection 객체를 얻는다.
3. Connection 객체로 부터 쿼리를 수행하기 위한 PreparedStatement 객체를 받는다.
4. executeQuery()를 수행하여 그 결과로 ResultSet 객체를 받아서 데이터를 처리한다.
5. 처리가 완료되면 처리에 사용된 리소스들을 close()하여 반환한다.

이 과정 중, 비용이 가장 많이 드는 단계는 WAS와 데이터베이스가 최초로 연결되어 Connection 객체가 만들어지는 부분이다. 

## DBCP
DBCP(Database Connection Pool)은 데이터베이스 Connection 객체를 효율적으로 운용하는 방법이다. 커넥션 풀을 이용하면 애플리케이션으로 들어오는 모든 HTTP 요청에 대해 매번 Connection 객체를 만들고 해제하는 것이 아니라, 미리 Connection 객체들을 만들어 두고 HTTP 요청이 들어올 때 Connection 객체를 가져다 쓰고 반환하는 방식으로 동작한다. 
 
데이터베이스의 커넥션 풀은 다음 순서로 동작한다.
1. WAS가 실행되면서 데이터베이스와 연결된 Connection 객체들을 일정량 생성해서 커넥션 풀에 저장해둔다.
   - WAS가 사용할 커넥션 풀 구현체(Apache의 Commons DBCP, Tomcat-JDBC, HikariCP, ...)를 설정할 수 있다. 
2. 데이터베이스 요청이 발생하면 풀에서 Connection 객체를 가져와서 쓴다.
3. 처리가 모두 끝나면 Connection을 풀에 반환한다.

만약 커넥션 풀의 모든 Connection이 사용 중이고 남은 Connection이 없는 상태에서 새로운 데이터베이스 요청이 들어오면 어떻게 될까? Connection이 반환될 때까지 대기 상태로 전환되며 순차적으로 Connection을 제공한다.

## DBCP 설정
- initialSize : 최초 DriverManager.getConnection() 메서드를 통해 커넥션 풀에 채워 넣을 Connection 개수
- maxActive : 동시에 사용할 수 있는 최대 Connection 개수(기본값: 8)
- maxIdle : 커넥션 풀에 반납할 때 최대로 유지될 수 있는 Connection 개수(기본값: 8)
- minIdle : 최소한으로 유지할 Connection 개수(기본값: 0)

## 장점
커넥션 풀을 이용하면 매 연결마다 HTTP 요청마다 데이터베이스 드라이버를 로드하고 Connection 객체를 생성하고 소멸시키는 비용을 줄일 수 있다. 미리 생성된 Connection 객체를 사용하기 때문에 데이터베이스로 접근하는 시간도 단축된다. 데이터베이스에 접근하는 Connection의 개수를 제한하여 데이터베이스와 서버 메모리에 걸리는 부하를 조정할 수 있다.

## 단점
Connection도 '객체'이기 때문에 메모리를 차지한다. WAS의 설정 중 스레드와 커넥션 풀의 개수는 메모리와 직접적으로 관련이 있기 때문에 성능에 많은 영향을 줄 수 있다. Connection 개수를 너무 많이 잡으면 커넥션 풀이 메모리를 많이 점유하게 되고 심지어 노는 Connection이 생길 수도 있다. 반대로 Connection 개수를 너무 적게 잡으면 서버에서 많은 요청을 처리하지 못한다. 따라서 적절한 커넥션 풀 크기를 설정하는 것이 중요하다.

## 참고
https://www.holaxprogramming.com/2013/01/10/devops-how-to-manage-dbcp/  