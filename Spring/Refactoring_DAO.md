# Refactoring DAO
토비의 스프링 3.1 Vol.1 1장 DAO 리팩터링하기

## 관심사의 분리
오브젝트에 대한 설계와 이를 구현한 코드는 끊임없이 바뀐다. 그래서 개발자가 객체를 설계할 때 가장 중요한 것은 바로 미래의 변화를 어떻게 대비할 것인가이다. 가장 좋은 대책은 변화의 폭을 최소한으로 줄여주는 것이다. 프로그래밍의 기초 개념 중에 관심사의 분리(Separation of Concerns)라는 게 있다. 이를 객체지향에 적용해보면, 관심이 같은 것끼리는 하나의 객체 안으로 모이게 하고, 관심이 다른 것은 가능한 따로 떨어져서 서로 영향을 주지 않도록 분리하는 것이다.

## Step 0: 간단한 DAO
사용자 정보를 JDBC API를 통해 DB에 저장하고 조회할 수 있는 간단한 DAO이다.

### User.java
```java
public class User {
    String id;
    String name;
    String password;
    
    public String getId() {
        return id;
    }
    
    public void setId(String id) {
        this.id = id;
    }
    
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        this.name = name;
    }
    
    public String getPassword() {
        return password;
    }
    
    public void setPassword(String password) {
        this.password = password;
    }
}
```

### UserDao.java
```java
public class UserDao {
    public void add(User user) throws ClassNotFoundException, SQLException {
        Class.forName("com.mysql.jdbc.Driver");
        Connection c = DriverManager.getConnection(
                "jdbc:mysql://localhost/springbook", "spring", "book");

        PreparedStatement ps = c.prepareStatement(
                "insert into users(id, name, password) values(?,?,?)");
        ps.setString(1, user.getId());
        ps.setString(2, user.getName());
        ps.setString(3, user.getPassword());
        
        ps.executeUpdate();
        
        ps.close();
        c.close();
    }
    
    public User get(String id) throws ClassNotFoundException, SQLException {
        Class.forName("com.mysql.jdbc.Driver");
        Connection c = DriverManager.getConnection(
                "jdbc:mysql://localhost/springbook", "spring", "book");

        PreparedStatement ps = c.prepareStatement(
                "select * from users where id = ?");
        ps.setString(1, id);
        
        ResultSet rs = ps.executeQuery();
        rs.next();
        User user = new User();
        user.setId(rs.getString("id"));
        user.setName(rs.getString("name"));
        user.setPassword(rs.getString("password"));
        
        rs.close();
        ps.close();
        c.close();
        
        return user;
    }
}
```

### UserDaoTest.java
```java
public class UserDaoTest {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        UserDao dao = new UserDao();
        
        User user = new User();
        user.setId("whiteship");
        user.setName("백기선");
        user.setPassword("married");
        
        dao.add(user);

        System.out.println(user.getId() + " 등록 성공");
        
        User user2 = dao.get(user.getId());
        System.out.println(user2.getName());
        System.out.println(user2.getPassword());

        System.out.println(user2.getId() + " 조회 성공");
    }
}
```

### 문제점
1. 예외상황에 대한 처리가 전혀 없다.  
2. DB 커넥션을 가져오는 코드가 중복되었다.  

## Step 1: 중복 코드의 메서드 추출
현재 DB 커넥션을 가져오는 코드는 다른 관심사와 섞여있고, 섞여있으면서 중복되어 있다. 이렇게 하나의 관심사가 중복되어 있고, 여기저기 흩어져 있어서 다른 관심사와 얽혀 있으면 스파게티 코드가 된다. 따라서, 가장 먼저 할 일은 커넥션을 가져오는 중복된 코드를 분리하는 것이다.  

### UserDao.java
```java
public class UserDao {
    public void add(User user) throws ClassNotFoundException, SQLException {
        Connection c = getConnection(); //DB 연결 기능이 필요하면 getConnection() 메서드를 이용하게 한다.
        
        PreparedStatement ps = c.prepareStatement(
                "insert into users(id, name, password) values(?,?,?)");
        ps.setString(1, user.getId());
        ps.setString(2, user.getName());
        ps.setString(3, user.getPassword());

        ps.executeUpdate();

        ps.close();
        c.close();
    }

    public User get(String id) throws ClassNotFoundException, SQLException {
        Connection c = getConnection(); //DB 연결 기능이 필요하면 getConnection() 메서드를 이용하게 한다.
        
        PreparedStatement ps = c.prepareStatement(
                "select * from users where id = ?");
        ps.setString(1, id);

        ResultSet rs = ps.executeQuery();
        rs.next();
        User user = new User();
        user.setId(rs.getString("id"));
        user.setName(rs.getString("name"));
        user.setPassword(rs.getString("password"));

        rs.close();
        ps.close();
        c.close();

        return user;
    }
    
    private Connection getConnection() throws ClassNotFoundException, SQLException { // 중복된 코드를 독립적인 메서드로 만들어서 중복을 제거했다.
        Class.forName("com.mysql.jdbc.Driver");
        Connection c = DriverManager.getConnection(
                "jdbc:mysql://localhost/springbook", "spring", "book");
        return c;
    }
}
```

## Step 2: 상속을 통한 확장
서로 다른 회사에서 UserDao를 사용하고 싶어 한다. 문제는 N사와 D사가 각기 다른 종류의 DB를 사용하고 있고, DB 커넥션을 가져오는 데 있어 독자적인 방법을 사용하는 점이다. UserDao 소스코드를 제공해주지 않고도 고객 스스로 원하는 DB 커넥션 생성 방식을 적용해가면서 UserDao를 사용하게 할 수 있을까?

### UserDao.java
```java
public abstract class UserDao {
    public void add(User user) throws ClassNotFoundException, SQLException {
        Connection c = getConnection();
        //...
    }
    
    public User get(String id) throws ClassNotFoundException, SQLException {
        Connection c = getConnection();
        //...
    }
    
    public abstract Connection getConnection() throws ClassNotFoundException, SQLException; //구현 코드는 제거되고 추상 메서드로 바뀌었다. 메서드의 구현은 서브클래스가 담당한다.
}
```

### NUserDao.java
```java
public class NUserDao extends UserDao {
    public Connection getConnection() throws ClassNotFoundException, SQLException { //상속을 통해 확장된 getConnection() 메서드
        //N사 DB 커넥션 생성코드
    }
}
```

### DUserDao.java
```java
public class DUserDao extends UserDao {
    public Connection getConnection() throws ClassNotFoundException, SQLException { //상속을 통해 확장된 getConnection() 메서드
        //D사 DB 커넥션 생성코드
    }
}
```

### 문제점
1. 상속을 사용했다. 상속에는 많은 한계점이 있다. 자바는 클래스의 다중 상속을 허용하지 않는다. 단지, 커넥션 객체를 가져오는 방법을 분리하기 위해 상속구조로 만들어버리면, 후에 다른 목적으로 UserDao에 상속을 적용하기 힘들다.
2. 확장된 기능인 DB 커넥션을 생성하는 코드를 다른 DAO 클래스에 적용할 수 없다. UserDao 외의 DAO들이 계속 만들어진다면 그때 getConnection()의 구현 코드가 모든 DAO 클래스마다 중복되어 나타나는 심각한 문제가 발생한다.

## Step 3: 클래스의 분리
추상 클래스를 만들고 이를 상속한 서브클래스에서 변화가 필요한 부분을 바꿔 쓰게 했다. 이렇게 하면 서로 영향을 주지 않으면서 각각 필요한 시점에 독립적으로 변경할 수 있다. 그러나 단점이 많은 상속을 사용했다는 사실이 불편하게 느껴진다. 두 개의 관심사를 아예 독립시킨다.

### UserDao.java
```java
public class UserDao {
    private SimpleConnectionMaker simpleConnectionMaker;

    public UserDao() {
        simpleConnectionMaker = new SimpleConnectionMaker();
    }

    public void add(User user) throws ClassNotFoundException, SQLException {
        Connection c = simpleConnectionMaker.makeNewConnection();
        //...
    }

    public User get(String id) throws ClassNotFoundException, SQLException {
        Connection c = simpleConnectionMaker.makeNewConnection();
        //...
    }
}
```

### SimpleConnectionMaker.java
```java
public class SimpleConnectionMaker { //상속을 제거했기 때문에 추상 클래스로 만들 필요가 없다.
    public Connection makeNewConnection() throws ClassNotFoundException, SQLException {
        Class.forName("com.mysql.jdbc.Driver");
        Connection c = DriverManager.getConnection(
                "jdbc:mysql://localhost/springbook", "spring", "book");
        return c;
    }
}
```

### 문제점
1. UserDao의 코드가 SimpleConnectionMaker라는 특정 클래스에 종속되어 버린다. 상속을 사용했을 때 처럼 UserDao 코드의 수정 없이 DB 커넥션 생성 기능을 변경할 방법이 없다. 처음 문제로 되돌아와 버렸다.
2. DB 커넥션을 제공하는 클래스가 어떤 것인지를 UserDao가 구체적으로 알고 있어야 한다.

## Step 4: 인터페이스의 도입
클래스를 분리하면서도 이런 문제를 해결할 수는 없을까? 가장 좋은 해결책은 두 개의 클래스가 서로 긴밀하게 연결되지 않도록 중간에 추상적인 느슨한 연결고리를 만들어주는 것이다. 여기서 추상화를 사용한다. 추상화는 어떤 것들의 공통적인 성격을 뽑아내어 이를 따로 분리하는 작업이다. 자바가 추상화를 위해 제공하는 가장 유용한 도구는 바로 인터페이스다. 결국 오브젝트를 만들려면 구체적인 클래스 하나를 선택해야겠지만 인터페이스로 추상화해놓은 최소한의 통로를 통해 접근하는 쪽에서는 오브젝트를 만들 때 사용할 클래스가 무엇인지 몰라도 된다. 

### ConnectionMaker.java
```java
public interface ConnectionMaker {
    public Connection makeConnection() throws ClassNotFoundException, SQLException;
}
```

### DConnectionMaker.java
```java
public class DConnectionMaker implements ConnectionMaker {
    public Connection makeConnection() throws ClassNotFoundException, SQLException {
        //D사 DB 커넥션 생성 코드
    }
}
```

### UserDao.java
```java
public class UserDao {
    private ConnectionMaker connectionMaker; //인터페이스를 통해 오브젝트에 접근하므로 구체적인 클래스 정보를 알 필요가 없다.

    public UserDao() {
        connectionMaker = new DConnectionMaker(); //앗! 여기에 클래스 이름이 나온다.
    }

    public void add(User user) throws ClassNotFoundException, SQLException {
        Connection c = connectionMaker.makeConnection(); //인터페이스에 정의된 메서드를 사용하므로, 구현 클래스가 바뀐다고 해도 메서드 이름이 변경될 걱정은 없다.
        //...
    }

    public User get(String id) throws ClassNotFoundException, SQLException {
        Connection c = connectionMaker.makeConnection();
        //...
    }
}
```

### 문제점
1. 생성자 코드에 new DConnectionMaker()는 제거되지 않고 남아 있다. 다시 원점이다.

## Step 5: 관계설정 책임의 분리
여전히 UserDao에는 어떤 ConnectionMaker 구현 클래스를 사용할지를 결정하는 코드가 남아 있다. new DConnectionMaker()는 이미 독립적인 관심사를 담고 있다. 이 코드를 UserDao에서 분리하지 않으면 UserDao는 결코 독립적으로 확장 가능한 클래스가 될 수 없다. UserDao를 사용하는 오브젝트(예를 들면 UserDaoTest, DAO를 사용하는 Service 코드 등)에다가 이 관심사를 두자. 즉, UserDao를 사용하는 코드에서 UserDao를 사용하기 전에, 먼저 UserDao가 어떤 ConnectionMaker의 구현 클래스를 사용할지를 결정하도록 만들자. 

### UserDao.java
```java
public class UserDao {
    private ConnectionMaker connectionMaker;

    public UserDao(ConnectionMaker connectionMaker) {
        this.connectionMaker = connectionMaker;
    }

    public void add(User user) throws ClassNotFoundException, SQLException {
        Connection c = connectionMaker.makeConnection();
        //...
    }

    public User get(String id) throws ClassNotFoundException, SQLException {
        Connection c = connectionMaker.makeConnection();
        //...
    }
}
```

### UserDaoTest.java
```java
public class UserDaoTest {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        ConnectionMaker connectionMaker = new DConnectionMaker(); //UserDao가 사용할 ConnectionMaker 구현 클래스를 결정하고 오브젝트를 만든다.
        
        UserDao dao = new UserDao(connectionMaker);
    }
}
```

### UserDaoTest.java
```java
public class UserDaoTest {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        ConnectionMaker connectionMaker = new NConnectionMaker(); //N사에서는 NConnectionMaker 클래스를 사용한다.
        
        UserDao dao = new UserDao(connectionMaker);
    }
}
```

### 문제점
1. UserDaoTest는 UserDao의 기능이 잘 동작하는지를 테스트하려고 만든 것이다. 그런데 갑자기 어떤 ConnectionMaker 구현 클래스를 사용할지를 결정하는 기능을 떠맡게 되었다. 

## Step 6: 팩토리의 도입
UserDaoTest에 새로 생긴 관심사를 분리시키자. 분리될 기능은 UserDao와 ConnectionMaker 구현 클래스의 오브젝트를 만드는 것과, 그렇게 만들어진 두 개의 오브젝트가 연결되어 사용될 수 있도록 관계를 맺어주는 것이다.  

분리시킬 기능을 담당할 클래스를 만든다. 이 클래스의 역할은 객체의 생성 방법을 결정하고 그렇게 만들어진 오브젝트를 돌려주는 것인데, 이런 일을 하는 오브젝트를 흔히 팩토리(factory)라고 부른다.  

### DaoFactory.java
```java
public class DaoFactory {
    public UserDao userDao() {
        ConnectionMaker connectionMaker = new DConnectionMaker();
        UserDao userDao = new UserDao(connectionMaker);
        return userDao;

        //위 코드는 return new UserDao(new DConnectionMaker()); 로 줄일 수 있다.
    }
}
```

### UserDaoTest.java
```java
public class UserDaoTest {
    public static void main(String[] args) throws ClassNotFoundException, SQLException { 
        UserDao dao = new DaoFactory().userDao();
        //...
    }
}
```

### 문제점
1. DaoFactory에 AccountDao, MessageDao 등 UserDao가 아닌 다른 DAO의 생성 기능을 넣으면 어떻게 될까? 아래 코드처럼 ConnectionMaker 구현 클래스의 오브젝트를 생성하는 코드가 메서드마다 반복된다. 이렇게 되면 구현 클래스를 바꿀 때마다 모든 메서드를 일일히 수정해야 한다.  
```java
public class DaoFactory {
    public UserDao userDao() {
        return new UserDao(new DConnectionMaker());
    }

    public AccountDao accountDao() {
        return new AccountDao(new DConnectionMaker());
    }

    public MessageDao messageDao() {
        return new MessageDao(new DConnectionMaker());
    }
}
``` 

## Step 7: 오브젝트 팩토리의 활용
중복되는 코드를 별도의 메서드로 뽑아내자. 

### DaoFactory.java
```java
public class DaoFactory {
    public UserDao userDao() {
        return new UserDao(connectionMaker());
    }

    public AccountDao accountDao() {
        return new AccountDao(connectionMaker());
    }

    public MessageDao messageDao() {
        return new MessageDao(connectionMaker());
    }
    
    public ConnectionMaker connectionMaker() {
        return new DConnectionMaker(); //분리해서 중복을 제거한 ConnectionMaker 타입의 오브젝트 생성 코드
    }
}
```

## Step 8: 스프링에서 사용하기

### DaoFactory.java
```java
@Configuration
public class DaoFactory {
    @Bean
    public UserDao userDao() {
        return new UserDao(connectionMaker());
    }
    
    @Bean
    public ConnectionMaker connectionMaker() {
        return new DConnectionMaker();
    }
}
```

### UserDaoTest.java
```java
public class UserDaoTest {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        ApplicationContext context = new AnnotationConfigApplicationContext(DaoFactory.class);
        UserDao dao = context.getBean("userDao", UserDao.class);
        //...
    }
}
```

## 응용 1: 개발용, 운영용 DB 분리하기
모든 DAO는 생성 시점에 ConnectionMaker 타입의 오브젝트를 컨테이너로부터 제공받는다. 구체적인 사용 클래스 이름은 설정정보에 들어 있다. 각각의 설정정보에 해당하는 DaoFactory만 다르게 만들어두면 나머지 코드에는 전혀 손대지 않고 개발 시와 운영 시에 다른 런타임 오브젝트에 의존관계를 갖게 해준다.

### 개발용 ConnectionMaker 생성 코드
```java
@Bean
public ConnectionMaker connectionMaker() {
    return new LocalDBConnectionMaker();
}
```

### 운영용 ConnectionMaker 생성 코드
```java
@Bean
public ConnectionMaker connectionMaker() {
    return new ProductionDBConnectionMaker();
}
```

## 응용 2: DB 연결횟수 카운팅하기
DI 컨테이너에서는 간단하게 해결할 수 있다. DAO와 DB 커넥션을 만드는 오브젝트 사이에 연결횟수를 카운팅하는 오브젝트를 하나 더 추가하면 된다. 중요한 것은 ConnectionMaker 인터페이스를 구현해서 만든다는 점이다.  

지금 UserDao는 ConnectionMaker의 인터페이스에만 의존하고 있다. 따라서 ConnectionMaker 인터페이스를 구현하고 있다면 어떤 클래스든 DI가 가능하다. 그래서 UserDao 오브젝트가 DI 받는 대상의 설정을 조정해서 DConnection 오브젝트 대신 CountingConnectionMaker 오브젝트로 바꿔치기한다.  

### CountingConnectionMaker.java
```java
public class CountingConnectionMaker implements ConnectionMaker {
    int counter = 0;
    private ConnectionMaker realConnectionMaker;

    public CountingConnectionMaker(ConnectionMaker realConnectionMaker) {
        this.realConnectionMaker = realConnectionMaker;
    }

    public Connection makeConnection() throws ClassNotFoundException, SQLException {
        this.counter++; //자신의 관심사인 카운팅 작업 후,
        return realConnectionMaker.makeConnection(); //실제 DB 커넥션을 만드는 오브젝트의 makeConnection()을 호출해서 결과를 DAO에 돌려줘야 정상적으로 동작할 수 있다.
    }
    
    public int getCounter() {
        return this.counter;
    }
}
```

### CountingDaoFactory.java
```java
@Configuration
public class CountingDaoFactory {
    @Bean
    public UserDao userDao() {
        return new UserDao(connectionMaker()); //모든 DAO는 connectionMaker()에서 만들어지는 오브젝트를 DI받는다.
    }

    @Bean
    public ConnectionMaker connectionMaker() {
        return new CountingConnectionMaker(realConnectionMaker());
    }

    @Bean
    public ConnectionMaker realConnectionMaker() {
        return new DConnectionMaker();
    }
}
```

### UserDaoConnectionCountingTest.java
```java
public class UserDaoConnectionCountingTest {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        ApplicationContext context = new AnnotationConfigApplicationContext(DaoFactory.class);
        UserDao dao = context.getBean("userDao", UserDao.class);
        
        //DAO 사용 코드
        
        CountingConnectionMaker ccm = context.getBean("connectionMaker", CountingConnectionMaker.class); //DL(의존관계 검색)
        System.out.println("Connection counter : " + ccm.getCounter());
    }
}
```

## 결론
스프링은 DI를 편하게 사용할 수 있도록 도와주는 도구이면서 그 자체로 DI를 적극 활용한 프레임워크이다. 스프링을 공부하는 건 DI를 어떻게 활용해야 할지를 공부하는 것이기도 하다.

## 참고
토비의 스프링 3.1 Vol.1 1장
