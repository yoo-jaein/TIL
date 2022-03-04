# Garbage Collection

## 가비지 컬렉션
가비지 컬렉션은 메모리 관리 기법 중의 하나로, 프로그램이 동적으로 할당했던 메모리 영역 중에서 필요없게 된 영역을 해제하는 기능이다. 자바의 가비지 컬렉션은 Heap 영역에 저장된 불필요한 메모리를 정리해준다. C언어와 다르게 자바에서는 JVM의 가비지 컬렉터(Garbage Collector)가 가비지 컬렉션을 알아서 해준다.  

## 자바 Heap 영역
Heap 영역은 다음의 두 가지 영역으로 나뉜다.
- Young Generation : 새로 생성된 객체가 할당되는 영역이다. Young Generation에서 일어나는 GC를 Minor GC라 부른다.
- Old Generation : Young Generation에서 Reachable 상태를 유지하여 살아남은 객체가 존재하는 영역이다. Old Generation에서 일어나는 GC를 Major GC 혹은 Full GC라 부른다.

## 자바 GC의 기본 원리
자바 GC의 기본 원리는 Stop The World & Mark and Sweep이다. 

### Stop The World
Stop The World란 GC를 실행하기 위해 JVM이 애플리케이션 실행을 멈추는 것이다. GC가 일어날 때 GC를 실행하는 스레드를 제외한 모든 스레드는 중지(Suspend)되고 GC가 완료된 후 작업이 재개된다. 이는 애플리케이션의 성능과 직결되므로 일반적으로 성능 개선을 위한 GC 튜닝은 이 Stop The World 시간을 줄이는 것을 목적으로 한다. 

### Mark and Sweep
- Mark : 사용되는 메모리와 사용되지 않는 메모리를 식별하는 작업
- Sweep : 사용되지 않음으로 식별된 메모리를 해제하는 작업

Stop The World로 모든 작업을 중단시킨 다음, 가비지 컬렉터는 스택의 모든 변수와 힙의 Reachable 객체의 객체 그래프를 탐색하며 Mark 작업을 한다. 이후 Mark 되지 않은 Unreachable 객체들을 메모리에서 제거하는 Sweep 작업을 한다.

## 자바 GC의 종류
Minor GC, Major GC로 나뉜다.

### Minor GC
Young Generation에서 발생하는 GC다. Young Generation은 다시 Eden, Survivor0, Survivor1 영역으로 나뉜다. 
- Eden : 새로 생성된 객체가 할당되는 영역이다.
- Survivor0, Survivor1 : 최소 1번의 GC를 살아남은 객체가 존재하는 영역이다.

1. 객체가 처음 생성되면 Young Generation의 Eden 영역에 할당되는데, 이 Eden 영역이 꽉 차면 Minor GC가 발생한다. 
2. 이 때 사용되고 있는(Reachable) 객체는 Mark되며, Mark되지 않은(Unreachable) 객체들은 제거된다. 
3. 살아남은 객체만 Survivor0 또는 Survivor1 영역(둘 중 비어있는 영역)으로 이동한다. 
4. Eden 영역이 또 다시 꽉 차서 Minor GC가 발생하면 Unreachable 객체가 제거되고 살아남은 객체만 Survivor0 또는 Survivor1 영역으로 이동한다. 
5. 이 때 사용하던 영역이 아닌 비어있는 영역으로 이동한다. (Survivor0과 Survivor1 영역 중 한 곳은 반드시 항상 비어있다.) 
6. 객체가 Survivor0-Survivor1 영역을 넘어갈 때 객체의 Age가 증가한다. 
7. Survivor0-Survivor1 영역을 오가면서 오랫 동안 살아남은 객체(Age >= -XX:MaxTenuringThreshold, Java SE 8 default 15)들은 Old Generation으로 옮겨진다. 
8. Survivor에서 Old Generation으로 옮겨지는 것을 Promotion이라 한다.

### Major GC
Old Generation에서 발생하는 GC다. 

1. Young Generation에서 오랫 동안 살아남은 객체는 Old Generation으로 Promotion된다.
2. Old Generation이 꽉 차면 Major GC가 발생한다. Old Generation은 일반적으로 Young Generation보다 크다. 따라서 Minor GC보다 Major GC가 더 오래 걸린다.

## 참고
https://d2.naver.com/helloworld/1329  
https://ko.wikipedia.org/wiki/%EC%93%B0%EB%A0%88%EA%B8%B0_%EC%88%98%EC%A7%91_(%EC%BB%B4%ED%93%A8%ED%84%B0_%EA%B3%BC%ED%95%99)  
https://johngrib.github.io/wiki/java-gc-eden-to-survivor/  