# JobRepository
배치 잡 실행 중에 오류가 발생하면 어떻게 복구할 수 있을까? 실행 중 오류가 발생했을 때 무슨 처리를 하던 중이었는지 어떻게 알 수 있을까?  
그것은 스프링 배치가 잡이 실행될 때 잡의 상태를 JobRepository에 저장하고 관리하기 때문이다.

## 스키마
![이미지](https://docs.spring.io/spring-batch/docs/current/reference/html/images/meta-data-erd.png)  

JobRepository 내에는 여섯 개의 테이블이 존재한다.

- BATCH_JOB_INSTANCE
- BATCH_JOB_EXECUTION
- BATCH_JOB_EXECUTION_PARAMS
- BATCH_JOB_EXECUTION_CONTEXT
- BATCH_STEP_EXECUTION
- BATCH_STEP_EXECUTION_CONTEXT

### BATCH_JOB_INSTANCE
잡 파라미터와 함께 잡을 처음 실행하면 JobInstance 레코드가 BATCH_JOB_INSTANCE 테이블에 등록된다.  

| 필드             | 설명                                                                         |
|------------------|------------------------------------------------------------------------------|
| job_execution_id | 테이블의 기본 키                                                             |
| version          | optimistic lock에 사용되는 레코드 버전                                       |
| job_name         | 실행된 잡의 이름                                                             |
| job_key          | 잡 이름과 잡 파라미터의 해시 값. JobInstance를 고유하게 식별하는데 사용된다. |

### BATCH_JOB_EXECUTION
잡이 실행될 때마다 새 레코드가 BATCH_JOB_EXECUTION 테이블에 등록된다. 잡이 진행되는 동안 주기적으로 업데이트된다.  

| 필드             | 설명                                         |
|------------------|----------------------------------------------|
| job_execution_id | 테이블의 기본 키                             |
| version          | optimistic lock에 사용되는 레코드 버전       |
| job_instance_id  | BATCH_JOB_INSTANCE 테이블을 참조하는 외래 키 |
| create_time      | 레코드가 생성된 시간                         |
| start_time       | 잡 실행이 시작된 시간                        |
| end_time         | 잡 실행이 끝난 시간                          |
| status           | 잡 실행의 배치 상태                          |
| exit_code        | 잡 실행의 종료 코드                          |
| exit_message     | exit_code와 관련된 메시지, 스택 트레이스     |
| last_updated     | 레코드가 마지막으로 갱신된 시간              |

### BATCH_JOB_EXECUTION_PARAMS
잡이 실행될 때 함께 사용된 잡 파라미터들은 BATCH_JOB_EXECUTION_PARAMS 테이블에 등록된다. 잡에 전달된 모든 파라미터가 이 테이블에 저장된다.   

| 필드             | 설명                                           |
|------------------|------------------------------------------------|
| job_execution_id | 테이블의 기본 키                               |
| type_code        | 파라미터 값의 타입을 나타내는 문자열           |
| key_name         | 파라미터 이름                                  |
| string_val       | 타입이 String인 경우 파라미터의 값             |
| date_val         | 타입이 Date인 경우 파라미터의 값               |
| long_val         | 타입이 Long인 경우 파라미터의 값               |
| double_val       | 타입이 Double인 경우 파라미터의 값             |
| identifying      | 파라미터가 식별되었는지 여부를 나타내는 플래그 |

### BATCH_JOB_EXECUTION_CONTEXT
JobExecution의 ExecutionContext는 BATCH_JOB_EXECUTION_CONTEXT 테이블에 등록된다.  

| 필드               | 설명                           |
|--------------------|--------------------------------|
| job_execution_id   | 테이블의 기본 키               |
| short_context      | 트림 처리된 serialized_context |
| serialized_context | 직렬화된 ExecutionContext      |

### BATCH_STEP_EXECUTION
스텝의 시작, 완료, 상태에 대한 메타데이터는 BATCH_STEP_EXECUTION 테이블에 등록된다. Read 횟수, Process 횟수, Write 횟수, Skip 횟수 등의 모든 데이터가 이 테이블에 저장된다.  

| 필드               | 설명                                               |
|--------------------|----------------------------------------------------|
| step_execution_id  | 테이블의 기본 키                                   |
| version            | optimistic lock 에 사용되는 레코드 버전            |
| step_name          | 스텝 이름                                          |
| job_execution_id   | BATCH_JOB_EXECUTION 테이블을 참조하는 외래 키      |
| start_time         | 스텝 실행이 시작된 시간                            |
| end_time           | 스텝 실행이 끝난 시간                              |
| status             | 스텝의 배치 상태                                   |
| commit_count       | 스텝 실행 중에 커밋된 트랜잭션 수                  |
| read_count         | 읽은 아이템 수                                     |
| filter_count       | ItemProcessor가 null을 반환하여 필터링된 아이템 수 |
| write_count        | 기록된 아이템 수                                   |
| read_skip_count    | ItemReader에서 예외가 발생하여 스킵된 아이템 수    |
| process_skip_count | ItemProcessor에서 예외가 발생하여 스킵된 아이템 수 |
| write_skip_count   | ItemWriter에서 예외가 발생하여 스킵된 아이템 수    |
| rollback_count     | 스텝 실행에서 롤백된 트랜잭션 수                   |
| exit_code          | 스텝의 종료 코드                                   |
| exit_message       | 스텝 실행에서 반환된 메시지나 스택 트레이스        |
| last_updated       | 레코드가 마지막으로 갱신된 시간                    |

### BATCH_STEP_EXECUTION_CONTEXT
StepExecution의 ExecutionContext는 BATCH_STEP_EXECUTION_CONTEXT 테이블에 등록된다.

| 필드               | 설명                           |
|--------------------|--------------------------------|
| step_execution_id  | 테이블의 기본 키               |
| short_context      | 트림 처리된 serialized_context |
| serialized_context | 직렬화된 ExecutionContext      |

## 참고
https://docs.spring.io/spring-batch/docs/current/reference/html/schema-appendix.html  
스프링 배치 완벽 가이드 2/e  