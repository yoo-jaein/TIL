# Java command-line options
자바 커맨드 라인 옵션

## 기본 사용법

> java ```[options]``` classname ```[args]```  
> java ```[options]``` -jar filename ```[args]```

- options : 공백으로 구분되는 커맨드 라인 옵션  
- classname : 실행할 클래스 이름  
- filename : 호출할 JAR(Java archive) 파일의 이름. -jar 옵션과 함께 사용된다.  
- args : main() 메서드에 전달될 arguments. 공백으로 구분된다.  

## 옵션 종류

1. Standard options (표준 옵션) : JVM의 모든 구현체에서 지원된다. JRE 버전 확인, 클래스 경로 설정, 세부 출력 활성화 등과 같은 일반적인 작업에 사용된다.  

2. Non-standard options (비표준 옵션) : Java HotSpot 가상 머신에 특정되는 범용 옵션이다. 모든 JVM 구현체에서 지원되는 것은 아니다. 비표준 옵션은 -X로 시작한다.  

3. Advanced options (고급 옵션) : Advanced runtime options, Advanced JIT compiler options, Advanced serviceability options, Advanced garbage collection options가 해당된다. 일상적인 용도로 사용되는 옵션이 아니다. 시스템 구성 매개변수에 대한 권한이 필요할 수 있으며, Java HotSpot 가상 머신 작업의 특정 영역을 조정하는데 사용되는 개발자 옵션이다. 모든 JVM 구현체에서 지원되는 것은 아니다. 고급 옵션은 -XX로 시작한다.

## 주요 옵션
### Standard options
- -jar filename  
jar 파일에 들어있는 프로그램을 실행한다.

- -D  
시스템 프로퍼티 값을 설정한다.

- -classpath  
클래스를 찾을 때 사용하는 경로인 클래스 패스를 지정한다.

- -verbose  
실행 정보를 화면에 출력한다.

- -version  
현재 JVM의 버전을 출력한다.

### Non-standard options
- -Xms  
JVM이 사용 가능한 최소 힙 사이즈를 설정한다.

- -Xmx  
JVM이 사용 가능한 최대 힙 사이즈를 설정한다.

### Advanced options
- -XX:PermSize  

- -XX:MaxPermSize  

## 참고
https://docs.oracle.com/javase/8/docs/technotes/tools/windows/java.html  
