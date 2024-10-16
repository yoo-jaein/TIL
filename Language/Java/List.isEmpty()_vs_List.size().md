# List.isEmpty() vs List.size()
자바의 List에서 원소의 존재 유무를 알아내기 위해 isEmpty() 또는 size() == 0을 사용할 수 있다.  
같은 의미로 보이는 두 방법 중 어떤 것을 사용하는 것이 좋을까?

## isEmpty()
isEmpty() 메서드는 List의 크기가 0인지 확인한다. 항상 O(1)의 시간 복잡도를 가진다.

## size()
size() 메서드는 List의 모든 원소들을 확인하여 원소의 개수를 반환한다. 대부분 O(1)의 시간 복잡도를 가지지만 때때로는 O(N)의 시간 복잡도를 가진다.

## 결론
isEmpty()를 사용하자.  
isEmpty()는 명확하여 size()보다 가독성이 좋고, 어떤 경우 size()보다 동작 속도가 빠르다.  

## 참고
https://dzone.com/articles/reasons-call-isempty  