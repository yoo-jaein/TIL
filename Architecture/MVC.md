# MVC

MVC는 하나의 애플리케이션을 Model, View, Controller의 세 가지 논리적 부분으로 나눠서 구현하는 아키텍처 패턴이다. 초기에는 그래픽 유저 인터페이스(GUI)를 위한 아키텍처 패턴으로 사용되었고 현재는 웹 애플리케이션 설계에서 주로 사용된다.  

MVC는 애플리케이션의 구조에 대한 아키텍처 패턴이다. 흔히 MVC 디자인 패턴이라고도 부르지만, 디자인 패턴은 특정 기술에 대한 문제를 해결하기 위해 사용하는 반면 아키텍처 패턴은 아키텍처에서의 문제를 해결할 목적으로 전체 구조에 영향을 미치기 때문에 MVC 아키텍처 패턴이라고 부르는 것이 조금 더 정확하다.

## MVC 구성 요소
MVC에는 세 가지 주요 구성 요소가 있다.
- Model
- View
- Controller

사용자가 Controller를 조작하면 Controller는 Model을 통해서 데이터를 가져오고 그 정보를 바탕으로 시각적인 표현을 담당하는 View를 제어해서 사용자에게 전달하게 됩니다. 

### Model
> data storage, integrity, consistency, queries, mutations

애플리케이션의 모든 데이터를 관리하고 데이터와 관련된 논리를 처리한다. 정보의 가공을 책임지는 컴포넌트다. 실제 데이터베이스에 연결되어 있다. 데이터의 상태가 변경되면 View에게 알려준다. Controller로부터 사용자의 입력을 받아서 그 처리 결과를 Controller에게 다시 전달한다.

### View
> presentation assets, graphic user interface

사용자에게 데이터를 보여준다. 사용자의 상호 작용을 처리한다. 웹 페이지에서 HTML/CSS 부분에 해당한다. Model에서 수집한 데이터로 페이지를 보여준다.

### Controller
> receive input, interpret input, validate input, create view, update view, query model, modify model

사용자의 입력을 수신하고 Model 또는 View에 적절한 메시지를 보낸다. Model과 View 사이에서 중개자 역할을 한다. Controller는 Model에게 무엇을 해야 하는지 알려준다. Model에서 데이터를 받고 처리하고 모든 정보를 가져와서 Controller에게 전달한다. 그 정보는 Controller가 다시 View에게 전달하고 사용자에게 어떻게 표현해야 할지 설명한다.

### MVC 확장
MVC를 확장한 MVVM(Model-View-Viewmodel), MVP(Model-View-Presenter), MVW(Model-View-Whatever) 등 다양한 패턴이 있다.

## MVC를 사용하는 이유
하나의 애플리케이션을 사용자가 보는 페이지(View), 데이터 처리(Model), 중간 제어(Controller) 3가지로 구성하면 애플리케이션이 작게 분리되어 각자의 역할에 집중할 수 있게 된다. 사용자 인터페이스를 변경하려면 View만 수정하고, 데이터의 처리 로직을 변경하려면 Model만 변경하면 된다.  

## MVC의 장점
- MVC 아키텍처는 사용자 인터페이스를 비즈니스 논리에서 분리한다.
- 관심사의 명확한 분리(SoC, Separation of Concerns)를 제공한다.
- 각 구성 요소를 독립적으로 테스트하는 데 도움이 된다.
- 구성 요소를 재사용할 수 있다.
- 확장과 유지 보수가 쉽다.

## 참고
https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller  
https://towardsdatascience.com/everything-you-need-to-know-about-mvc-architecture-3c827930b4c1  
https://m.blog.naver.com/jysaa5/221751719334  
