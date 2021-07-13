# StepExecutionListener
잡 리스너와 마찬가지로 스텝 리스너도 있다. StepExecutionListener는 스텝의 시작과 끝에서 특정 로직을 처리할 수 있게 해주고, ChunkListener는 청크의 시작과 끝에서 특정 로직을 처리할 수 있게 해준다. 둘 다 JobExecutionListner 인터페이스의 메서드와 유사한 메서드를 제공한다. beforeStep, afterStep, beforeChunk, afterChunk 메서드가 존재한다. 인터페이스 구현을 간단히 할 수 있도록 @BeforeStep, @AfterStep, @BeforeChunk, @AfterChunk 어노테이션도 제공한다.

## 참고
스프링 배치 완벽 가이드 2/e  