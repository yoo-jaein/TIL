# Apache tika
Apache Software Foundation에서 관리하는 콘텐츠 감지 및 분석 프레임워크

## 활용
업로드하는 파일의 확장자는 보안상 프론트와 서버에서 모두 체크해줘야 한다. 그런데 단순히 파일 확장자명을 체크하는 것만으로는 충분하지 않다. 예를 들어, 원래 jpg 파일이었던 것을 확장자만 csv로 바꿔서 업로드할 수도 있다. 이런 케이스를 대비해서 파일의 메타데이터를 체크해줘야 한다. 자바에서는 ```Apache tika```를 비롯한 라이브러리를 이용하면 파일의 확장자명을 임의로 변경해도 메타데이터를 통해 ```text/plain```과 같은 ```MimeType```('파일 종류'/'파일 포맷')을 알아낼 수 있다.

## tika
- 코어 라이브러리인 ```tika-core```에는 Tika의 주요 인터페이스와 클래스가 포함되어 있다. 파일 확장자를 체크하기 위한 목적이라면 ```tika-core``` 의존성만으로도 충분하다.
- ```tika-parsers```에는 ```tika-core```를 포함하여 문서를 파싱할 수 있는 인터페이스와 클래스가 포함되어 있다.

## 의존성 추가하기
### build.gradle
```groovy
// https://mvnrepository.com/artifact/org.apache.tika/tika-core
implementation 'org.apache.tika:tika-core:2.1.0'
```

### build.gradle
```groovy
// https://mvnrepository.com/artifact/org.apache.tika/tika-parsers
implementation 'org.apache.tika:tika-parsers:2.1.0'
```

## 파일 확장자 알아내기
```java
public String findMimeType(File file) throws IOException {
    Tika tika = new Tika();
    String mimeType = tika.detect(file);
    return mimeType;
}
```
- ```detect()```를 통해 파일의 ```MimeType```을 ```String``` 데이터로 구할 수 있다.

### tika-mimetypes.xml
```xml
<mime-type type="image/jpeg">
<mime-type type="image/png">
<mime-type type="text/csv">
<mime-type type="text/dns"/>
<mime-type type="text/ecmascript"/>
<mime-type type="text/enriched"/>
...
```
- ```detect()```의 리턴 결과인 ```MimeType```의 종류는 ```tika-mimetypes.xml```에서 확인할 수 있다.

## 참고
https://mvnrepository.com/artifact/org.apache.tika  
