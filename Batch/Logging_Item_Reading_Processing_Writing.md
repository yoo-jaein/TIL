# Logging Item Reading / Processing / Writing
스프링 배치에서 아이템 Read, Process, Write할 때 로그를 찍는 방법

## ItemReadListener
```java
import org.springframework.batch.core.ItemReadListener;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class MemberReadListener implements ItemReadListener<Member> {

	@Override
	public void beforeRead() {
		log.debug("MemberReadListener - beforeRead");
	}

	@Override
	public void afterRead(Member item) {
		log.debug("MemberReadListener - Reading Member id={}", item.getId());
	}

	@Override
	public void onReadError(Exception ex) {
		log.error("MemberReadListener - Error message={}", ex.getMessage());
	}

}
```

## ItemProcessListener
```java
import org.springframework.batch.core.ItemProcessListener;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class MemberProcessListener implements ItemProcessListener<Member, String> {
    
	@Override
	public void beforeProcess(Member item) {
	}

	@Override
	public void afterProcess(Member item, String result) {
	}

	@Override
	public void onProcessError(Member item, Exception e) {
		log.error("MemberProcessListener - Error message={}", ex.getMessage());
	}
}
```

## ItemWriteListener
```java
import org.springframework.batch.core.ItemWriteListener;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class MemberWriteListener implements ItemWriteListener<String> {
    
	@Override
	public void beforeWrite(List<? extends String> items) {
	}

	@Override
	public void afterWrite(List<? extends String> items) {
	}

	@Override
	public void onWriteError(Exception exception, List<? extends String> items) {
        log.error("MemberWriteListener - Error message={}", ex.getMessage());
	}
}
```

## Step 설정
```java
@Bean
public Step step() {
    return stepBuilderFactory.get("MemberJobStep")
        .<Member, String>chunk(10)
        .reader(reader)
        .processor(processor)
        .writer(writer)
        .listener(new MemberReadListener())
        .listener(new MemberProcessListener())
        .listener(new MemberWriteListener())
        .build();
}
```

## 참고
https://docs.spring.io/spring-batch/docs/current/reference/html/common-patterns.html#loggingItemProcessingAndFailures  

