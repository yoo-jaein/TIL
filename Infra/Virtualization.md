# Virtualization

## 등장 배경
예전에는 IT 인프라를 필요에 따라 개별적으로 구축했다. 서버 컴퓨터의 용량이 부족하다면 더 큰 컴퓨터로 옮기고, 네트워크 회선이 부족하다면 회선을 추가적으로 설치했다. 하지만 이렇게 개별적으로 조달하는 방식으로는 급격하게 변화하는 세상에 빠르게 대응할 수 없다. 이런 상황을 벗어나기 위한 기술이 바로 '가상화'이다.


## 가상화

가상화란 CPU 처리 능력, 스토리지 용량, 네트워크 기능 등을 지정하여 시스템을 자유롭게 만들고 구성을 변경하는 기술을 말한다.  
이때 서버나 스토리지, 네트워크의 물리적인 실체와는 다르지만 실질적인 기능은 동일하다.  
사용자의 입장에서는 물리적인 내부 상황은 알 필요가 전혀 없다. 필요한 기능, 원하는 기능만 제대로 실행되면 되기 때문이다.

## 가상화의 종류

1. 파티셔닝  
하나의 시스템 자원을 여러 개의 독립된 자원으로 기능하게 만든다.
예를 들면 1대의 서버를 10대의 개별적으로 독립된 서버가 존재하고 있는 것처럼 만드는 경우이다.  


2. 어그리게이션  
여러 개의 시스템 자원이 하나의 시스템 자원처럼 기능하게 만든다.  
예를 들면 물리적으로 다른 여러 개의 스토리지를 하나의 큰 스토리지처럼 보이게 하는 경우이다.


3. 에뮬레이션  
어떤 시스템 자원을 다른 시스템 자원으로 기능하게 만든다.  
예를 들면 PC에서 스마트폰의 OS를 가동시키고 스마트폰의 화면을 표시하는 경우이다. (블루스택)

## 가상화와 컨테이너
가상화된 컴퓨터는 OS나 설정 데이터를 각각 가지고 움직이기 때문에 시스템의 자원을 대량으로 소비한다.  
그래서 똑같은 OS의 핵심을 공유하면서 각각 분리된 애플리케이션 실행 환경을 제공하는 '컨테이너' 방식이 주목을 받게 된다.  
컨테이너 방식이 보급되면서 시스템 자원도 절약하고 변경에도 즉시 대응할 수 있게 되었다.