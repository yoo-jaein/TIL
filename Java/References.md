# References
자바 참조

## 1. 강한 참조 Strong References

참조 객체의 기본 유형이다. 활성상태의 강한 참조가 있는 객체는 가비지 컬렉션의 대상이 될 수 없다. 객체는 강하게 참조된 변수가 null을 가리킬 때만 가비지 컬렉션된다.

```java
Apple apple = new Apple();
```

여기서 'apple' 객체는 새로 생성된 Apple 인스턴스에 대한 강한 참조이며, 현재 obj는 활성 객체이므로 가비지 컬렉션이 불가능하다.

```java
apple = null; // 'apple' 객체는 더 이상 인스턴스를 참조하지 않는다.
```
이제 apple 객체가 null을 가리키므로 가비지 컬렉션의 대상이 될 수 있다.

## 2. 약한 참조 Weak References

```java
// java.lang.ref.WeakReference<T>
WeakReference<Apple> weakApple = new WeakReference<Apple>(apple);
```

WeakReference 클래스를 이용하여 생성한다. apple = null 되면 해당 객체를 가리키는 참조가 WeakReference만 남게 된다. JVM이 약한 참조만 있는 객체를 감지하면 (즉, 객체에 연결된 강한 참조/소프트 참조가 없는 경우) 이 객체는 가비지 컬렉션 대상으로 표시된다. 다음 GC가 발생하는 시점에 무조건 메모리에서 제거된다.

## 3. 소프트 참조 Soft References

```java
// java.lang.ref.SoftReference<T>
SoftReference<Apple> softApple = new SoftReference<Apple>(apple);
```

SoftReference 클래스를 이용하여 생성한다. apple = null 되면 참조가 SoftReference만 남게 된다. 소프트 참조가 약한 참조와 다른 점은, 소프트 참조에서는 객체가 가비지 컬렉션 대상이 될 수 있더라도 JVM에 메모리가 부족하기 전까지 수집되지 않는다는 것이다. JVM에 메모리가 부족하면 그 때 객체가 메모리에서 지워진다. 이 특징때문에 소프트 참조는 일부 캐시 라이브러리에 사용되는 것으로 알려져있다.  

가비지 컬렉터(GC)는 호출되면 힙의 모든 요소를 반복한다. 그리고 특수한 참조 큐에 참조 유형 객체를 저장한다. GC는 힙의 모든 객체를 확인한 후, 큐에서 제거해야 할 객체를 결정하고 제거한다. JVM 구현마다 다르지만 소프트 참조는 JVM이 OutOfMemoryError를 던지기 전에 지워지는 것이 보장된다. 소프트 참조가 지워지는 시간이나 지워지는 순서는 보장되지 않는다. 일반적으로 JVM 구현은 최근에 생성된 참조(recently-created) 또는 최근에 사용한 참조(recently-used)의 정리 중에서 선택한다.  

소프트 참조 객체는 마지막으로 참조된 후 일정 시간동안 활성 상태로 유지된다. 기본값은 힙의 여유 메가바이트당 수명 1초이다. 이 값은 -XX:SoftRefLRUPolicyMSPerMB 플래그를 사용하여 조정할 수 있다. 예를 들어 값을 2.5초로 변경하려면 다음을 사용할 수 있다.  

```text
-XX:SoftRefLRUPolicyMSPerMB=2500
```

약한 참조와 비교하여 소프트 참조는 추가 메모리가 필요할 때까지 계속 존재하기 때문에 수명이 더 길 수 있다. 따라서 가능한 오랫동안 객체를 메모리에 보관해야 하는 경우 더 나은 선택이다.  

## 4. 팬텀 참조 Phantom References

팬텀 참조는 일반적으로 잘 사용되지 않으며 SoftReference 또는 WeakReference와 상당히 다르다. 객체에 대한 그립이 너무 약해서 객체를 검색할 수도 없다. get() 메서드는 항상 null을 반환한다. 팬텀 참조의 용도는 객체가 메모리에서 삭제되는 시점을 추적하는 것이다.

## 참고
https://www.geeksforgeeks.org/types-references-java/  
https://www.baeldung.com/java-weak-reference  
https://www.baeldung.com/java-soft-references  
http://web.archive.org/web/20140530004941/http://weblogs.java.net/blog/enicholas/archive/2006/05/understanding_w.html  