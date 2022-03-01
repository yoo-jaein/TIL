# Flow
java.util.concurrent.Flow

```java
package java.util.concurrent;

public final class Flow {

    private Flow() {} // uninstantiable

    @FunctionalInterface
    public static interface Publisher<T> {
        public void subscribe(Subscriber<? super T> subscriber);
    }

    public static interface Subscriber<T> {
        public void onSubscribe(Subscription subscription);
        public void onNext(T item);
        public void onError(Throwable throwable);
        public void onComplete();
    }

    public static interface Subscription {
        public void request(long n);
        public void cancel();
    }

    public static interface Processor<T,R> extends Subscriber<T>, Publisher<R> {
    }

    static final int DEFAULT_BUFFER_SIZE = 256;

    public static int defaultBufferSize() {
        return DEFAULT_BUFFER_SIZE;
    }
}
```

자바 9에서는 리액티브 프로그래밍을 제공하는 클래스 java.util.concurrent.Flow를 추가했다. Flow 클래스는 중첩된 인터페이스 4개를 포함하고 있으며 인스턴스화할 수 없다. 

## Publisher<T>
Publisher는 이벤트를 발행한다. Subscriber의 요구에 따라 백프레셔 기법에 의해 이벤트 제공 속도가 제한된다. 

## Subscriber<T>
Subscriber는 한 개씩 또는 한 번에 여러 항목을 소비한다. Publisher가 발행한 이벤트의 리스너로 자신을 등록할 수 있다. Publisher가 이벤트를 발행할 때 호출할 수 있도록 콜백 메서드 4개(onSubscribe, onNext, onError, onComplete)를 정의한다. 이 이벤트들은 프로토콜에서 정한 다음의 순서를 따라야 한다.

```text
onSubscribe -> onNext* -> (onError | onComplete)?
```

onSubscribe 메서드가 항상 첫 번째로 호출된다. 이어서 onNext가 여러 번 호출될 수 있다. 이벤트 스트림은 영원히 지속되거나 아니면 onComplete 콜백을 통해 종료될 수 있다. 또는 Publisher에 장애가 발생했을 때 onError를 호출할 수 있다.  

Subscriber가 Publisher에 자신을 등록할 때 Publisher는 처음으로 onSubscribe 메서드를 호출해 Subscription 객체를 전달한다.   

## Subscription
Publisher와 Subscriber 사이의 제어 흐름, 백프레셔를 관리한다. Subscription 인터페이스는 메서드 2개를 정의한다. request는 Pulisher에게 주어진 개수의 이벤트를 처리할 준비가 되었음을 알린다. cancel은 Subscription을 취소하여 Publisher에게 더 이상 이벤트를 받지 않음을 통지한다.

## Processor<T,R>
Processor는 Publisher와 Subscriber를 상속받을 뿐 아무 메서드도 추가하지 않는다. 이 인터페이스는 [리액티브 스트림](https://github.com/yoo-jaein/TIL/blob/main/Spring/Spring_WebFlux.md)에서 처리하는 이벤트의 변환 단계를 나타낸다. Processor가 에러를 수신하면 회복하거나 즉시 onError 신호로 모든 Subscriber에 에러를 전파할 수 있다. 마지막 Subscriber가 Subscription을 취소하면 Processor는 자신의 업스트림 Subscription도 취소하여 취소 신호를 전파한다.

## Flow의 용도
Flow 클래스에 정의된 인터페이스 대부분은 직접 구현하도록 의도된 것이 아니다. Flow는 표준화 과정을 위해 탄생한 클래스다. Flow API를 만들 당시, 이미 Akka, RxJava 등 다양한 리액티브 스트림의 구현체 라이브러리들이 존재했다. 이 라이브러리들은 원래 동일한 발행-구독(pub-sub) 패러다임에 기반해 리액티브 프로그래밍을 구현했지만, 각자 독립적으로 개발되다보니 서로 다른 이름규칙과 API를 사용했다. 그래서 자바 9에서 Flow를 통해 Akka, RxJava 등 리액티브 라이브러리에서 준수해야 할 규칙과 리액티브 애플리케이션들이 서로 협력하기 위한 공용어를 제시했다. 

## 참고
모던 자바 인 액션 17장 리액티브 프로그래밍
