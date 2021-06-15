# How to concat String and stringValue()
Querydsl의 쿼리에서 문자열과 stringValue()를 연결하는 방법

## 방법 1
```java
String imageDomain = "https://github.com/yoo-jaein/TIL/";

select(Expressions.asString(imageDomain).concat(member.imageUrl.stringValue()))
//결과: https://github.com/yoo-jaein/TIL/1234
```

Expressions를 static import할 수도 있다.

```java
select(asString(imageDomain).concat(member.imageUrl.stringValue()))
```

## 방법 2
```java
select(member.username.concat("_").concat(member.age.stringValue()))
// 결과: member_24
```
