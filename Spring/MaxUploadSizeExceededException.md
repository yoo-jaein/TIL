# MaxUploadSizeExceededException

```text
multipart.MaxUploadSizeExceededException: Maximum upload size exceeded; nested exception is java.lang.IllegalStateException: org.apache.tomcat.util.http.fileupload.FileUploadBase$FileSizeLimitExceededException: The field sourceFile exceeds its maximum permitted size of 1048576 bytes. 
```

- 업로드 파일의 최대 사이즈는 기본적으로 1048576 바이트다. 이 크기를 초과하는 파일을 업로드할 경우 MaxUploadSizeExceededException이 발생한다.

## 해결 방법
### application.yml
```yaml
spring:
  servlet:
    max-file-size: 10MB
    max-request-size: 10MB
```
