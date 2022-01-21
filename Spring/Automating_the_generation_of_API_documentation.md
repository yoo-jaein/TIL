# Automating the generation of API documentation
API 문서 자동화

## Spring Rest Docs
[Spring Rest Docs](https://github.com/spring-projects/spring-restdocs)


## Swagger
[Swagger](https://github.com/swagger-api/swagger-ui)


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
https://springdoc.org/  