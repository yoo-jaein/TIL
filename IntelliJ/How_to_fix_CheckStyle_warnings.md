# How to fix CheckStyle warnings

IntelliJ에서 기존에 있던 프로젝트를 클론한 후 새로운 코드 컨벤션과 함께 CheckStyle을 적용했다. 그랬더니 프로젝트 파일 전체가 무수히 많은 CheckStyle warning으로 뒤덮이게 되었다. 이 문제를 해결하기 위해 사용했던 방법들을 기록해본다.

코드 컨벤션 파일과 CheckStyle의 적용 과정은 생략한다.

## 문자열 연결할 때
### [space-around-binary-tenary-operator] '+' is not followed by whitespace

![image](https://postfiles.pstatic.net/MjAyMTA2MDhfNTIg/MDAxNjIzMTYwMjE1ODQz.jfv2yAPfWW9yCyCSNuWpChz8KvZ2TDotmS9JV-MkEuEg.o2FaxmL7qQq_msZV0qfTmNG3bKvA9aqaA2JUjEMXUL4g.PNG.y_jaein/image.png?type=w966)  

```java
//올바른 예시
@Query("SELECT m "
		+ "FROM Member" 
		+ "m WHERE m.username = :username")
```
- '+' 문자 뒤에 한 칸씩 공백을 넣는다.
- " 문자 뒤에 공백을 넣지 않는다.
- warning이 사라지지 않으면 문자열의 공백을 모두 지우고 한 줄로 만든뒤 다시 개행을 넣어본다.

## 자바 변수의 키워드 순서
### [modifier-order] 'static' modifier out of order with the JLS suggestions

```java
//올바른 예시
public static final String string = "test.string";
```

- JLS(Java Language Specification)에서 권장하는 키워드 순서를 따라야 한다.
1. [ public | protected | private ]

2. static

3. abstract

4. synchronized

5. [ transient | volatile ]

6. final

7. native

8. strictfp

9. [ int | long | String | class | enum | interface etc. ]

## 여러 파일의 개행문자 수정
### [newline-lf] Line must end with LF, not CRLF

![image](https://postfiles.pstatic.net/MjAyMTA2MDhfMjkg/MDAxNjIzMTYwMjM5ODYw.PerPJdkXk-bQdq6TxB63wGRRSW98ZMt1gpF9Azfbt4Yg.p55lAzDWm9s26bhFJWDstIwJEa0BAhEs7ney7zupqnog.PNG.y_jaein/image.png?type=w966)  

구글링해보니 .gitattributes 파일으로 CRLF 개행 문자 문제를 해결할 수도 있다고 한다. 나는 IntelliJ에서 직접 파일 설정을 변경하는 방식을 사용했다.  

1. Project 탭에서 디렉토리나 파일을 선택한다.  
![image](https://postfiles.pstatic.net/MjAyMTA2MDhfMjk3/MDAxNjIzMTYwMjU5NTc1.4dJfGOIg4B73H9JTJmeW1MB_hcLbkxJE0ww3tz6_uAwg.JtT7yz2roDI5l4OlEGBOnVEjo4nZCwcYqCn9UXczDrAg.PNG.y_jaein/image.png?type=w966)


2. File-File Properties-Line Separators로 들어간다.
3. 원하는 개행문자를 선택한다.  
![image](https://postfiles.pstatic.net/MjAyMTA2MDhfOTAg/MDAxNjIzMTYwMjY5NDcw.B9Cdgu5gWR2KdU--caWxqzVRfb9QyqIvE8_bK4eckwAg.VyCNiWOStXAvcsR5t06zHy1TSdMzu70nVm1QQAHk2fgg.PNG.y_jaein/image.png?type=w966)  


4. 오른쪽 하단에서 Line Separator가 바뀐 것을 확인한다.  
![image](https://postfiles.pstatic.net/MjAyMTA2MDhfMTAx/MDAxNjIzMTYwMjk2MDc4.MnflwDaiyn7UFzEjv8IjZV5co2vK9wPZL5MoGgM9YPQg.cbMVmyG3eZgIBmIi7xAghPsu1gW0QcE5t5l_7DDxb-Ag.PNG.y_jaein/image.png?type=w966)  
이 Line Separator 버튼으로 파일 하나의 개행 문자를 수정할 수도 있다.   

## 여러 파일의 인덴트(탭) 수정
### [indentation-tab] Indent must use tab characters

![image](https://postfiles.pstatic.net/MjAyMTA2MDhfMTQg/MDAxNjIzMTYwMzA3MTIx.Mn7Vp9z91WlK97yotGb4FdGXvKFzdX2Boyv4MRYw3vIg.aenfa53q2jaB3zEWea1EmA0kvIxJlSBpjae4jSl5mEUg.PNG.y_jaein/image.png?type=w966)  

파일 하나의 인덴트를 변경하는 방법과 한 번에 여러 파일의 인덴트를 변경하는 방법이 있다.

1. 현재 파일의 인덴트를 수정하는 방법
	1. Ctrl + Shift + A를 누른다.  
	2. 검색 창에서 "To Tabs"를 입력한다.
    3. 적용을 확인한다.
    

2. 여러 파일의 인덴트를 한 번에 수정하는 방법
    1. Project 탭에서 디렉토리나 파일을 우클릭한다.  
    2. Reformat Code를 선택한다.  
       ![image](https://postfiles.pstatic.net/MjAyMTA2MDhfMjM5/MDAxNjIzMTYwMzMwOTEy.oHr61sNLOoNE2ssVU9mVWk5E0iFwAlcICb2AXUUyAvcg.40jmqJRlbk8IuxStFU1MLJUSGSWOWxQRwiaAK1cJAO8g.PNG.y_jaein/image.png?type=w966)
    3. Rearrange entries 옵션을 선택하고 Run을 누른다.  
       ![image](https://postfiles.pstatic.net/MjAyMTA2MDhfMTUw/MDAxNjIzMTYwMzU4Nzk3.cWAmw1LBsQwjxDzEh7_FFKnq66J1CTsBSqTIbcZ-KBEg.kCDAbkvGEs_t11s_fzeAmJTI6g4SuY9QuoBpNOLSs3sg.PNG.y_jaein/image.png?type=w966)
    4. 적용을 확인한다.  
    이 방법은 프로젝트에 적용한 코드 컨벤션 파일의 설정으로 코드를 재포맷하는 것으로 보인다. 따라서 적절한 컨벤션 파일이 이미 적용된 상태여야 할 것이다.

## 참고
https://stackoverflow.com/questions/10299067/modifier-keyword-order-in-java  
https://www.jetbrains.com/help/idea/configuring-line-endings-and-line-separators.html  
https://stackoverflow.com/questions/50915842/intellij-how-to-convert-indents-for-all-files  
https://stackoverflow.com/questions/8534819/how-can-i-use-tabs-for-indentation-in-intellij-idea