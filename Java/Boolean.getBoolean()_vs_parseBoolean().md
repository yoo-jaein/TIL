# Boolean.getBoolean() vs parseBoolean()
java.lang.Boolean.getBoolean(), parseBoolean(), valueOf()

## Boolean.getBoolean()
```java
Boolean.getBoolean("true") == false
Boolean.getBoolean("True") == false
Boolean.getBoolean("false") == false
```

```java
public static boolean getBoolean(String name) {
	boolean result = false;
	try {
		result = parseBoolean(System.getProperty(name));
	} catch (IllegalArgumentException | NullPointerException e) {
	}
	return result;
}
```

> 해당 인수에 의해 명명된 시스템 속성이 존재하고 'true' 문자열과 동일한 경우에만 true를 반환한다.

Boolean.getBoolean() 메서드는 Boolean.valueOf() 또는 Boolean.parseBoolean() 메서드와 혼동하는 경우가 많다. 대부분 Boolean.getBoolean()이 String 인수가 나타내는 Boolean 값을 반환하는 것으로 생각하기 쉽기 때문에 자주 오용된다.  

인수에 의해 명명된 시스템 속성이 존재하고 대소문자를 무시하고 문자열 "true"와 동일한 경우에만 true를 반환합니다. 시스템 속성은 System 클래스에서 정의한 메서드인 getProperty를 통해 액세스할 수 있습니다.  
지정된 이름의 속성이 없거나 지정된 이름이 비어 있거나 null이면 false가 반환됩니다.  
- Params: name – 시스템 속성 이름입니다.
- Returns: 시스템 속성의 부울 값.
- Throws: SecurityException – 보안 관리자가 존재하고 해당 checkPropertyAccess 메서드가 지정된 시스템 속성에 대한 액세스를 허용하지 않는 경우.
- 참고: System.getProperty(String), System.getProperty(String, String)

## Boolean.parseBoolean()
```java
Boolean.parseBoolean("true") == true
Boolean.parseBoolean("True") == true
Boolean.parseBoolean("false") == false
Boolean.parseBoolean("yes") == false
```

```java
public static boolean parseBoolean(String s) {
	return "true".equalsIgnoreCase(s);
}
```

문자열 인수를 부울로 구문 분석합니다. 반환된 부울 값은 문자열 인수가 null이 아니고 대소문자를 무시하고 문자열 "true"와 동일한 경우 true 값을 나타냅니다. 그렇지 않으면 null 인수를 포함하여 false 값이 반환됩니다.  

예: Boolean.parseBoolean("True")은 true를 반환합니다. 예: Boolean.parseBoolean("yes")은 false를 반환합니다.  
매개변수: s – 구문 분석할 부울 표현을 포함하는 문자열  
보고: 문자열 인수가 나타내는 부울  

## Boolean.valueOf()
```java
Boolean.valueOf("true") == true
Boolean.valueOf("True") == true
Boolean.valueOf("false") == false
Boolean.valueOf("yes") == false
```

```java
public static Boolean valueOf(String s) {
	return parseBoolean(s) ? TRUE : FALSE;
}
```

지정된 문자열이 나타내는 값으로 부울을 반환합니다. 반환된 부울은 문자열 인수가 null이 아니고 대소문자를 무시하고 문자열 "true"와 동일한 경우 true 값을 나타냅니다. 그렇지 않으면 null 인수를 포함하여 false 값이 반환됩니다.  
매개변수: s - 문자열.  
보고: 문자열이 나타내는 부울 값입니다.  
