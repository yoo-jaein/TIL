# Automating the generation of API documentation
API 문서 자동화

## Spring Rest Docs
[Spring Rest Docs](https://spring.io/projects/spring-restdocs)는 RESTful 서비스를 쉽게 문서화하는 프로젝트다. [Asciidoctor](http://asciidoctor.org/)를 사용하여 작성된 문서와 테스트 실행으로 자동 생성된 스니펫을 결합시켜 문서를 만들 수 있다. Swagger같은 도구에서 생성된 완전 자동화된 문서가 아니라 GitHub의 API 문서처럼 읽기 쉬운 가이드를 만들기 위한 라이브러리다.

- Spring MVC의 Test framework, Spring WebFlux의 WebTestClient 또는 REST Assured 3로 작성된 테스트로 스니펫을 생성한다. 생성된 스니펫과 손으로 작성한 문서를 결합하여 RESTful 서비스를 문서화한다. 이러한 테스트 중심 접근은 문서의 정확성을 보장해준다. 만약 스니펫이 정확하지 않다면 테스트가 실패할 것이다.
- 스니펫을 결합하는데 Asciidoctor를 사용한다. Asciidoctor는 AsciiDoc®을 문서 모델로 구문 분석하고 HTML 5, DocBook 5, PDF 및 기타 출력 형식으로 변환하기 위한 Ruby 기반의 빠른 오픈 소스 텍스트 프로세서다.

### 0. 샘플 어플리케이션
https://docs.spring.io/spring-restdocs/docs/current/reference/#getting-started-sample-applications

### 1. 최소 조건
다음의 최소 조건을 갖춰야 한다.
- Java 8 
- Spring Framework 5 (5.0.2 or later)

### 2. 의존성 추가
#### pom.xml
```xml
<dependency>
  <groupId>org.springframework.restdocs</groupId>
  <artifactId>spring-restdocs-mockmvc</artifactId>
  <version>{project-version}</version>
  <scope>test</scope>
</dependency>

<build>
<plugins>
  <plugin>
    <groupId>org.asciidoctor</groupId>
    <artifactId>asciidoctor-maven-plugin</artifactId>
    <version>1.5.8</version>
    <executions>
      <execution>
        <id>generate-docs</id>
        <phase>prepare-package</phase>
        <goals>
          <goal>process-asciidoc</goal>
        </goals>
        <configuration>
          <backend>html</backend>
          <doctype>book</doctype>
        </configuration>
      </execution>
    </executions>
    <dependencies>
      <dependency>
        <groupId>org.springframework.restdocs</groupId>
        <artifactId>spring-restdocs-asciidoctor</artifactId>
        <version>{project-version}</version>
      </dependency>
    </dependencies>
  </plugin>
</plugins>
</build>
```

#### build.gradle
```groovy
plugins { 
    id "org.asciidoctor.jvm.convert" version "3.3.2"
}

configurations {
    asciidoctorExt 
}

dependencies {
    asciidoctorExt 'org.springframework.restdocs:spring-restdocs-asciidoctor:{project-version}' 
    testImplementation 'org.springframework.restdocs:spring-restdocs-mockmvc:{project-version}' 
}

ext { 
    snippetsDir = file('build/generated-snippets')
}

test { 
    outputs.dir snippetsDir
}

asciidoctor { 
    inputs.dir snippetsDir 
    configurations 'asciidoctorExt' 
    dependsOn test 
}
```

### 3. 테스트 코드 작성
기본적으로 Spring REST Docs는 문서화하는 페이로드가 JSON이라고 가정한다. JSON이 아닌 XML 페이로드를 문서화하려면 request 또는 response의 콘텐츠 유형이 application/xml과 호환되어야 한다.
```json
{
    "contact": {
        "name": "Jane Doe",
        "email": "jane.doe@example.com"
    }
}
```

#### Request



#### Response
```java
this.webTestClient.get().uri("user/5").accept(MediaType.APPLICATION_JSON)
    .exchange().expectStatus().isOk().expectBody()
    .consumeWith(document("user",
        responseFields(
            fieldWithPath("contact.email").description("The user's email address"), 
            fieldWithPath("contact.name").description("The user's name")))); 
```


### 4. 스니펫 생성
테스트가 성공했을 경우, 기본적으로 다음의 6가지 스니펫이 자동 생성된다.
- <output-directory>/index/curl-request.adoc : curl 커맨드를 포함한다.
- <output-directory>/index/http-request.adoc : 테스트의 HTTP request를 포함한다.
- <output-directory>/index/http-response.adoc : 테스트 결과의 HTTP response를 포함한다.
- <output-directory>/index/httpie-request.adoc : 테스트의 HTTPie 커맨드를 포함한다.
- <output-directory>/index/request-body.adoc : 보내진 request의 body를 포함한다.
- <output-directory>/index/response-body.adoc : 리턴된 response의 body를 포함한다.

### 5. Asciidoc 파일 구성
Asciidoctor의 매크로를 사용하여 스니펫을 가져오고 Asciidoc 파일을 구성한다.
```adoc
[[example_curl_request]]
== Curl request

include::{snippets}/index/curl-request.adoc[]

[[example_http_request]]
== HTTP request

include::{snippets}/index/http-request.adoc[]

[[example_http_response]]
== HTTP response

include::{snippets}/index/http-response.adoc[]
```


## Swagger
[Swagger](https://swagger.io/)


## Springdoc OpenAPI
[springdoc-openapi](https://github.com/springdoc/springdoc-openapi)는 스프링 부트 프로젝트에서 API 문서를 자동으로 생성할 수 있게 도와주는 자바 라이브러리다. 스프링 설정, 클래스 구조 및 다양한 어노테이션을 기반으로 런타임에 API 의미를 추론하여 애플리케이션을 검사한다. HTML, JSON, YAML 포맷을 지원한다.

- HTML 문서
  - 공식 [swagger-ui jar](https://github.com/swagger-api/swagger-ui.git)를 사용하여 HTML 형식 문서를 제공한다.
  - http://server:port/context-path/swagger-ui.html

- JSON 문서
  - http://server:port/context-path/v3/api-docs

- YAML 문서
  - http://server:port/context-path/v3/api-docs.yaml

- server: 서버 이름 또는 IP
- port: 서버 포트
- context-path: 애플리케이션의 컨텍스트 경로

### 1. 의존성 추가

#### pom.xml
```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-ui</artifactId>
    <version>last-release-version</version>
</dependency>
```

#### build.gradle
```groovy
implementation 'org.springdoc:springdoc-openapi-ui:1.6.4'
```

### 1.5. 커스텀 경로 설정
이 단계는 선택 사항이다. HTML 형식의 swagger 문서 경로를 커스텀하고 싶다면 스프링 부트 구성 파일에 springdoc.swagger-ui.path 속성을 추가하면 된다.

```yaml
# swagger-ui custom path
springdoc:
  swagger-ui:
    path: /swagger-ui.html
```

### 2. 문서 생성

#### GUI HTML
1. 애플리케이션 실행
2. http://localhost:8080/swagger-ui/index.html#/ 접속

#### JSON
1. 애플리케이션 실행
2. http://localhost:8080/v3/api-docs/ 접속
```yaml
{
  "openapi": "3.0.1",
  "info": {
    "title": "OpenAPI definition",
    "version": "v0"
  },
  "servers": [
    {
      "url": "http://localhost:8080/",
      "description": "Generated server url"
    }
  ],
  "paths": {
    "/approval": {
      "put": {
        "tags": [
          "book-controller"
        ],
        "operationId": "putBook",
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/BookRequest"
              }
            }
          },
          "required": true
...
```

#### yaml
1. 애플리케이션 실행
2. http://localhost:8080/v3/api-docs.yaml 접속
```yaml
openapi: 3.0.1
info:
  title: OpenAPI definition
  version: v0
servers:
- url: http://localhost:8080
  description: Generated server url
paths:
  /approval:
    put:
      tags:
      - book-controller
      operationId: putBook
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/BookRequest'
        required: true
      responses:
        "200":
```

## 참고
https://spring.io/projects/spring-restdocs#overview  
https://docs.spring.io/spring-restdocs/docs/current/reference/  
https://springdoc.org/  