# @Transactional
스프링의 트랜잭션

## @Transactional
트랜잭션을 구현하는 방법은 두 가지가 있다. 하나는 Programmatic Transaction, 다른 하나는 Declarative Transaction이다. Programmatic Transaction(프로그래밍적 트랜잭션)은 말 그대로 프로그래밍할 때 트랜잭션 관련 로직을 포함하는 것이다. 이 방식은 트랜잭션에 대한 코드가 중복되고 코드 유지보수가 어렵다는 단점이 있다. Declarative Transaction(선언적 트랜잭션)은 비즈니스 로직에서 트랜잭션 관련 로직을 분리시키는 방법이다. 스프링에서는 AOP를 사용해서 Declarative Transaction을 구현하고 있다. 아래 내용에서 설명하는 @Transactional 트랜잭션은 Declarative Transaction에 해당한다.

```java
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
@Transactional
public class TransactionService {
	public void transaction1() {}
	public void transaction2() {}
}
```
```java
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class TransactionService {
	@Transactional
	public void transaction1() {}
	public void transaction2() {}
}
```
비즈니스 로직에서 트랜잭션을 적용하고 싶다면 @Transactional을 사용하면 된다. 이 어노테이션을 메서드에 적용할 경우 메서드 단위로 트랜잭션이 처리되고, 클래스에 적용할 경우 그 클래스가 제공하는 모든 메서드에 대해 트랜잭션이 처리된다. 클래스와 메서드에 @Transactional이 붙어있는 경우 해당 클래스에 트랜잭션이 적용된 프록시 객체가 생성된다. 프록시 객체는 @Transactional이 포함된 메서드가 호출되기 전/후에 트랜잭션을 시작하고 종료될 때 커밋이나 롤백을 수행한다. 기본적으로 CheckedException이 발생하거나 예외가 없을 때는 커밋하고, UncheckedException이 발생하면 롤백한다.

## AOP Proxy
스프링은 AOP를 사용해서 프록시 객체를 만든다. AOP는 여러 코드에 존재하는 공통된 부가기능 코드를 독립적으로 분리해주는 프로그래밍 기법이다. 프록시를 만드는 방법은 JDK Dynamic Proxy와 CGLib 방식이 있다. 스프링 부트 2.0 버전 부터는 CGLib이 기본 설정으로 되어있다.

### JDK Dynamic Proxy
타겟이 인터페이스를 상속하는 경우 사용하는 방법이다. Java의 Reflection 패키지의 Proxy 클래스를 통해 프록시 객체를 생성한다. Dynamic Proxy 객체는 타겟이 상속하고 있는 인터페이스를 상속한 다음 추상메서드를 구현해서 내부적으로 타겟 메서드 호출 전 후에 트랜잭션 처리를 수행한다. 프록시 객체를 런타임 시점에 동적으로 만들기 때문에 Dynamic Proxy라 한다.

### CGLib
타겟이 인터페이스를 상속하지 않는 경우 사용하는 방법이다. 

## @Transactional 설정
```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface Transactional {

	@AliasFor("transactionManager")
	String value() default ""; // 사용할 트랜잭션의 관리자 설정
	
	@AliasFor("value")
	String transactionManager() default ""; 

	String[] label() default {}; // 트랜잭션을 설명할 라벨 설정

	Propagation propagation() default Propagation.REQUIRED; // 전파 설정

	Isolation isolation() default Isolation.DEFAULT; // 격리 수준 설정

	int timeout() default TransactionDefinition.TIMEOUT_DEFAULT; // 트랜잭션 타임아웃 (초단위) 설정

	String timeoutString() default ""; // 트랜잭션 타임아웃 문자열로 (초단위) 설정

	boolean readOnly() default false; // true: 읽기 전용 트랜잭션, false: 읽기/쓰기 트랜잭션

	Class<? extends Throwable>[] rollbackFor() default {}; // 롤백을 수행할 예외 클래스 설정

	String[] rollbackForClassName() default {}; // 롤백을 수행할 예외 클래스를 이름으로 설정

	Class<? extends Throwable>[] noRollbackFor() default {}; // 롤백을 수행하지 않을 예외 클래스 설정

	String[] noRollbackForClassName() default {}; // 롤백을 수행하지 않을 예외 클래스를 이름으로 설정
}
```
@Transactional 어노테이션은 트랜잭션을 위한 여러 설정을 지원한다. 그 중 트랜잭션의 전파 속성과 격리 수준에 대해 알아보자.

### 트랜잭션 전파 속성(Propagation)
- REQUIRED : 부모 트랜잭션 내에서 실행하며 부모 트랜잭션이 없을 경우 새로운 트랜잭션을 생성함.
- REQUIRES_NEW : 부모 트랜잭션을 일시 중단하고 새로운 트랜잭션을 생성함.
- SUPPORT : 부모 트랜잭션 내에서 실행하며 부모 트랜잭션이 없을 경우 트랜잭션 없이 실행함. 트랜잭션이 없지만 해당 경계 안에서 Connection 객체나 Hibernate의 Session 등은 공유할 수 있음.
- MANDATORY : 부모 트랜잭션 내에서 실행되며 부모 트랜잭션이 없을 경우 예외가 발생함.
- NOT_SUPPORT : 부모 트랜잭션 내에서 실행될 경우 일시 중단하고 트랜잭션 없이 실행함.
- NEVER : 부모 트랜잭션이 존재한다면 예외가 발생하며 트랜잭션 없이 실행함.
- NESTED : 현재 트랜잭션이 있으면 중첩된 트랜잭션 내에서 실행하고, 그렇지 않으면 REQUIRED처럼 동작한다.

### 트랜잭션 격리 수준(Isolation)
- DEFAULT : 사용하는 데이터베이스 드라이버의 기본 설정을 사용함. 대부분 READ_COMMITTED를 기본 값으로 사용함.
- READ_UNCOMMITTED : 하나의 트랜잭션이 커밋되기 전의 변화가 다른 트랜잭션에 그대로 노출됨.
- READ_COMMITTED : 다른 트랜잭션이 커밋하지 않은 정보는 읽을 수 없음. 1번 트랜잭션이 읽은 로우를 2번 트랜잭션에서 수정하고 커밋했을 때, 1번 트랜잭션이 해당 로우를 다시 읽을 경우 값이 달라지는 상황이 일어날 수 있음.
- REPEATABLE_READ : 하나의 트랜잭션이 읽은 로우를 다른 트랜잭션이 수정할 수 없도록 막아줌. 새로운 로우를 추가하는 것은 막지 않음. 1번 트랜잭션이 특정 조건으로 여러 로우를 읽었는데 2번 트랜잭션에서 검색 조건에 해당하는 새로운 로우를 추가할 경우, 1번 트랜잭션이 다시 그 조건으로 검색했을 때 새로운 로우가 발견될 수 있음.
- SERIALIZABLE : 트랜잭션을 순차적으로 진행함. 여러 트랜잭션이 동시에 같은 테이블의 로우에 접근할 수 없음.

## 트랜잭션 관련 코드 살펴보기
### TransactionManager
```java
package org.springframework.transaction;

public interface TransactionManager { }
```
스프링의 트랜잭션 매니저 구현을 위한 마커 인터페이스다.

### PlatformTransactionManager
```java
package org.springframework.transaction;

public interface PlatformTransactionManager extends TransactionManager {
	TransactionStatus getTransaction(@Nullable TransactionDefinition definition) throws TransactionException;
	void commit(TransactionStatus status) throws TransactionException;
	void rollback(TransactionStatus status) throws TransactionException;
}
```
스프링에서 트랜잭션을 구현하기 위한 인터페이스다. getTransaction()은 TransactionDefinition에 지정된 전파 속성(Propagation behaviors)에 따라 현재 활성화된 트랜잭션을 반환하거나 새로운 트랜잭션을 생성한다. commit()은 주어진 트랜잭션을 커밋한다. rollback()은 주어진 트랜잭션을 롤백한다.  

### TransactionDefinition
```java
package org.springframework.transaction;

public interface TransactionDefinition {

	int PROPAGATION_REQUIRED = 0;
	int PROPAGATION_SUPPORTS = 1;
	int PROPAGATION_MANDATORY = 2;
	int PROPAGATION_REQUIRES_NEW = 3;
	int PROPAGATION_NOT_SUPPORTED = 4;
	int PROPAGATION_NEVER = 5;
	int PROPAGATION_NESTED = 6;
	int ISOLATION_DEFAULT = -1;
	int ISOLATION_READ_UNCOMMITTED = 1;  // same as java.sql.Connection.TRANSACTION_READ_UNCOMMITTED;
	int ISOLATION_READ_COMMITTED = 2;  // same as java.sql.Connection.TRANSACTION_READ_COMMITTED;
	int ISOLATION_REPEATABLE_READ = 4;  // same as java.sql.Connection.TRANSACTION_REPEATABLE_READ;
	int ISOLATION_SERIALIZABLE = 8;  // same as java.sql.Connection.TRANSACTION_SERIALIZABLE;
	int TIMEOUT_DEFAULT = -1;

	default int getPropagationBehavior() {
		return PROPAGATION_REQUIRED;
	}

	default int getIsolationLevel() {
		return ISOLATION_DEFAULT;
	}

	default int getTimeout() {
		return TIMEOUT_DEFAULT;
	}
	
	default boolean isReadOnly() {
		return false;
	}
	
	@Nullable
	default String getName() {
		return null;
	}
	
	static TransactionDefinition withDefaults() {
		return StaticTransactionDefinition.INSTANCE;
	}
}
```
스프링에서 사용하는 트랜잭션의 전파 속성, 격리 수준, 타임아웃 시간, readonly 속성을 정의하는 인터페이스다. 

```java
public class DefaultTransactionDefinition implements TransactionDefinition, Serializable {
	...
	public DefaultTransactionDefinition(TransactionDefinition other) {
		this.propagationBehavior = other.getPropagationBehavior(); // PROPAGATION_REQUIRED
		this.isolationLevel = other.getIsolationLevel(); // ISOLATION_DEFAULT
		this.timeout = other.getTimeout(); // TIMEOUT_DEFAULT
		this.readOnly = other.isReadOnly(); // false
		this.name = other.getName(); // null
	}
	...
}
```
DefaultTransactionDefinition의 생성자에서 TransactionDefinition의 default 메서드들을 사용하고 있는 것을 확인할 수 있다.

### TransactionStatus
```java
package org.springframework.transaction;

public interface TransactionStatus extends TransactionExecution, SavepointManager, Flushable {

	boolean hasSavepoint();

	@Override
	void flush();
}
```
PlatformTransactionManager의 getTransaction() 메서드는 TransactionStatus 타입을 리턴한다. TransactionStatus은 트랜잭션의 상태를 나타낸다. 

```java
package org.springframework.transaction.support;

public class DefaultTransactionStatus extends AbstractTransactionStatus {
	...
	public DefaultTransactionStatus(
		@Nullable Object transaction, boolean newTransaction, boolean newSynchronization,
		boolean readOnly, boolean debug, @Nullable Object suspendedResources) {

		this.transaction = transaction;
		this.newTransaction = newTransaction;
		this.newSynchronization = newSynchronization;
		this.readOnly = readOnly;
		this.debug = debug;
		this.suspendedResources = suspendedResources;
	}
	...
}
```
DefaultTransactionStatus의 생성자에서 트랜잭션과 관련된 다양한 상태 정보를 받아서 저장한다.

### AbstractPlatformTransactionManager
```java
package org.springframework.transaction.support;

public abstract class AbstractPlatformTransactionManager implements PlatformTransactionManager, Serializable {
	...
	@Override
	public final TransactionStatus getTransaction(@Nullable TransactionDefinition definition) throws TransactionException {

		// TransactionDefinition이 없는 경우 기본값을 사용한다.
		TransactionDefinition def = (definition != null ? definition : TransactionDefinition.withDefaults());

		Object transaction = doGetTransaction(); // 트랜잭션을 가져온다. 이 다음 코드부터 타임아웃과 전파 동작에 따라 로직이 처리된다.
		boolean debugEnabled = logger.isDebugEnabled();

		if (isExistingTransaction(transaction)) {
			// 기존 트랜잭션이 발견되었다면, 전파 동작을 확인하여 동작 방법을 찾는다.
			return handleExistingTransaction(def, transaction, debugEnabled);
		}

		if (def.getTimeout() < TransactionDefinition.TIMEOUT_DEFAULT) {
			throw new InvalidTimeoutException("Invalid transaction timeout", def.getTimeout());
		}

		// 기존 트랜잭션을 찾을 수 없다면, 진행 방법을 알아보기 위해 전파 동작을 확인한다.
		if (def.getPropagationBehavior() == TransactionDefinition.PROPAGATION_MANDATORY) {
			throw new IllegalTransactionStateException(
				"No existing transaction found for transaction marked with propagation 'mandatory'");
		}
		else if (def.getPropagationBehavior() == TransactionDefinition.PROPAGATION_REQUIRED ||
			def.getPropagationBehavior() == TransactionDefinition.PROPAGATION_REQUIRES_NEW ||
			def.getPropagationBehavior() == TransactionDefinition.PROPAGATION_NESTED) {
			SuspendedResourcesHolder suspendedResources = suspend(null);
			if (debugEnabled) {
				logger.debug("Creating new transaction with name [" + def.getName() + "]: " + def);
			}
			try {
				return startTransaction(def, transaction, debugEnabled, suspendedResources);
				// 새로운 트랜잭션이 생성된다.
			}
			catch (RuntimeException | Error ex) {
				resume(null, suspendedResources);
				throw ex;
			}
		}
		...
	}
	
	@Override
	public final void commit(TransactionStatus status) throws TransactionException {
		if (status.isCompleted()) {
			throw new IllegalTransactionStateException(
				"Transaction is already completed - do not call commit or rollback more than once per transaction");
			// 트랜잭션이 이미 완료되었습니다. 트랜잭션당 두 번 이상 커밋 또는 롤백을 호출하지 마십시오.
		}

		DefaultTransactionStatus defStatus = (DefaultTransactionStatus) status;
		if (defStatus.isLocalRollbackOnly()) {
			if (defStatus.isDebug()) {
				logger.debug("Transactional code has requested rollback");
				// 트랜잭션 코드에서 롤백을 요청했다.
			}
			processRollback(defStatus, false);
			return;
		}

		if (!shouldCommitOnGlobalRollbackOnly() && defStatus.isGlobalRollbackOnly()) {
			if (defStatus.isDebug()) {
				logger.debug("Global transaction is marked as rollback-only but transactional code requested commit");
				// 전역 트랜잭션은 롤백 전용으로 표시되지만 트랜잭션 코드에서 커밋을 요청했다.
			}
			processRollback(defStatus, true); // 롤백을 수행한다.
			return;
		}

		processCommit(defStatus); // 커밋을 수행한다.
	}
	
	@Override
	public final void rollback(TransactionStatus status) throws TransactionException {
		if (status.isCompleted()) {
			throw new IllegalTransactionStateException(
				"Transaction is already completed - do not call commit or rollback more than once per transaction");
			// 트랜잭션이 이미 완료되었습니다. 트랜잭션당 두 번 이상 커밋 또는 롤백을 호출하지 마십시오.
		}

		DefaultTransactionStatus defStatus = (DefaultTransactionStatus) status;
		processRollback(defStatus, false); // 롤백을 수행한다.
	}
	...
	private void processCommit(DefaultTransactionStatus status) throws TransactionException {
		try {
			boolean beforeCompletionInvoked = false;

			try {
				boolean unexpectedRollback = false;
				prepareForCommit(status);
				triggerBeforeCommit(status);
				triggerBeforeCompletion(status);
				beforeCompletionInvoked = true;

				if (status.hasSavepoint()) {
					if (status.isDebug()) {
						logger.debug("Releasing transaction savepoint");
					}
					unexpectedRollback = status.isGlobalRollbackOnly();
					status.releaseHeldSavepoint();
				}
				else if (status.isNewTransaction()) {
					if (status.isDebug()) {
						logger.debug("Initiating transaction commit");
					}
					unexpectedRollback = status.isGlobalRollbackOnly();
					doCommit(status);
				}
				else if (isFailEarlyOnGlobalRollbackOnly()) {
					unexpectedRollback = status.isGlobalRollbackOnly();
				}

				// Throw UnexpectedRollbackException if we have a global rollback-only
				// marker but still didn't get a corresponding exception from commit.
				if (unexpectedRollback) {
					throw new UnexpectedRollbackException(
						"Transaction silently rolled back because it has been marked as rollback-only");
				}
			}
			catch (UnexpectedRollbackException ex) {
				// can only be caused by doCommit
				triggerAfterCompletion(status, TransactionSynchronization.STATUS_ROLLED_BACK);
				throw ex;
			}
			catch (TransactionException ex) {
				// can only be caused by doCommit
				if (isRollbackOnCommitFailure()) {
					doRollbackOnCommitException(status, ex);
				}
				else {
					triggerAfterCompletion(status, TransactionSynchronization.STATUS_UNKNOWN);
				}
				throw ex;
			}
			catch (RuntimeException | Error ex) {
				if (!beforeCompletionInvoked) {
					triggerBeforeCompletion(status);
				}
				doRollbackOnCommitException(status, ex);
				throw ex;
			}
			...
	}

	private void processRollback(DefaultTransactionStatus status, boolean unexpected) {
		try {
			boolean unexpectedRollback = unexpected;

			try {
				triggerBeforeCompletion(status);

				if (status.hasSavepoint()) {
					if (status.isDebug()) {
						logger.debug("Rolling back transaction to savepoint");
					}
					status.rollbackToHeldSavepoint();
				}
				else if (status.isNewTransaction()) {
					if (status.isDebug()) {
						logger.debug("Initiating transaction rollback");
					}
					doRollback(status);
				}
				...
	}
	...
	protected abstract void doCommit(DefaultTransactionStatus status) throws TransactionException;

	protected abstract void doRollback(DefaultTransactionStatus status) throws TransactionException;
	...
}
```
스프링에서 사용하는 트랜잭션 매니저에 대한 추상 기본 클래스(Abstract Base Class)다. 스프링의 표준 트랜잭션에 대한 워크플로우를 구성한다. 크게 다음의 역할을 수행한다.
- 기존 트랜잭션이 있는지 확인한다.
- 적절한 전파 동작을 적용한다.
- 필요한 경우 트랜잭션을 일시 중단하고(suspend) 재개한다(resume).
- 커밋 시 롤백 전용 플래그를 확인한다. 
- 롤백 시 적절한 수정 사항을 적용한다(실제 롤백 또는 롤백 전용 설정). 
- 등록된 동기화 콜백을 트리거한다(트랜잭션 동기화가 활성화된 경우). 

PlatformTransactionManager에 선언되었던 getTransaction(), commit(), rollback()을 구현하고 있다. commit()과 rollback()의 내부에는 processCommit(), processRollback()으로 커밋과 롤백을 처리한다. 그런데 processCommit()과 processRollback() 내부에서 추상 메서드인 doCommit(), doRollback()을 사용하고 있는 것을 볼 수 있다. 다양한 트랜잭션 매니저의 구현체(DataSourceTransactionManager, JpaTransactionManager, JtaTransactionManager 등)에서 이 doCommit()과 doRollback()을 구현하고 있다. 이렇게 구현체 별로 다른 동작을 하게 만드는 디자인 패턴을 템플릿 메서드 패턴릿(Template Method Pattern)이라 한다.

```java
package org.springframework.orm.jpa;

public class JpaTransactionManager extends AbstractPlatformTransactionManager
	implements ResourceTransactionManager, BeanFactoryAware, InitializingBean {
	...
	@Override
	protected void doCommit(DefaultTransactionStatus status) {
		JpaTransactionObject txObject = (JpaTransactionObject) status.getTransaction();
		if (status.isDebug()) {
			logger.debug("Committing JPA transaction on EntityManager [" +
				txObject.getEntityManagerHolder().getEntityManager() + "]");
		}
		try {
			EntityTransaction tx = txObject.getEntityManagerHolder().getEntityManager().getTransaction();
			tx.commit();
		}
		catch (RollbackException ex) {
			if (ex.getCause() instanceof RuntimeException) {
				DataAccessException dae = getJpaDialect().translateExceptionIfPossible((RuntimeException) ex.getCause());
				if (dae != null) {
					throw dae;
				}
			}
			throw new TransactionSystemException("Could not commit JPA transaction", ex);
		}
		catch (RuntimeException ex) {
			// Assumably failed to flush changes to database.
			throw DataAccessUtils.translateIfNecessary(ex, getJpaDialect());
		}
	}

	@Override
	protected void doRollback(DefaultTransactionStatus status) {
		JpaTransactionObject txObject = (JpaTransactionObject) status.getTransaction();
		if (status.isDebug()) {
			logger.debug("Rolling back JPA transaction on EntityManager [" +
				txObject.getEntityManagerHolder().getEntityManager() + "]");
		}
		try {
			EntityTransaction tx = txObject.getEntityManagerHolder().getEntityManager().getTransaction();
			if (tx.isActive()) {
				tx.rollback();
			}
		}
		catch (PersistenceException ex) {
			throw new TransactionSystemException("Could not roll back JPA transaction", ex);
		}
		finally {
			if (!txObject.isNewEntityManagerHolder()) {
				// Clear all pending inserts/updates/deletes in the EntityManager.
				// Necessary for pre-bound EntityManagers, to avoid inconsistent state.
				txObject.getEntityManagerHolder().getEntityManager().clear();
			}
		}
	}
	...
}
```
doCommit()과 doRollback()을 구현하고 있는 것을 확인할 수 있다.

### TransactionInterceptor
```java
package org.springframework.transaction.interceptor;

public class TransactionInterceptor extends TransactionAspectSupport implements MethodInterceptor, Serializable {
	...
	@Override
	@Nullable
	public Object invoke(MethodInvocation invocation) throws Throwable {
		// 타겟 클래스를 확인한다.
		// TransactionAttributeSource는 타겟 클래스와 (인터페이스에서 온 것일 수 있는) 메서드를 전달해야 한다.
		Class<?> targetClass = (invocation.getThis() != null ? AopUtils.getTargetClass(invocation.getThis()) : null);

		// TransactionAspectSupport의 invokeWithinTransaction()에 적용한다.
		return invokeWithinTransaction(invocation.getMethod(), targetClass, invocation::proceed);
	}
	...
}
```
스프링은 TransactionInterceptor를 통해 트랜잭션의 Aspect를 설정한다. MethodInterceptor의 invoke()를 구현해서 타겟 클래스를 확인하고 TransactionAspectSupport의 invokeWithinTransaction()를 호출한다.

### TransactionAspectSupport
```java
package org.springframework.transaction.interceptor;

public abstract class TransactionAspectSupport implements BeanFactoryAware, InitializingBean {
	...
	@Nullable
	protected Object invokeWithinTransaction(Method method, @Nullable Class<?> targetClass, 
		final InvocationCallback invocation) throws Throwable {

		// TransactionAttribute가 null인 경우 이 메서드는 트랜잭션이 아니다.
		TransactionAttributeSource tas = getTransactionAttributeSource();
		final TransactionAttribute txAttr = (tas != null ? tas.getTransactionAttribute(method, targetClass) : null);
		final TransactionManager tm = determineTransactionManager(txAttr);

		if (this.reactiveAdapterRegistry != null && tm instanceof ReactiveTransactionManager) {
			ReactiveTransactionSupport txSupport = this.transactionSupportCache.computeIfAbsent(method, key -> {
				if (KotlinDetector.isKotlinType(method.getDeclaringClass()) && KotlinDelegate.isSuspend(method)) {
					throw new TransactionUsageException(
						"Unsupported annotated transaction on suspending function detected: " + method +
							". Use TransactionalOperator.transactional extensions instead.");
				}
				ReactiveAdapter adapter = this.reactiveAdapterRegistry.getAdapter(method.getReturnType());
				if (adapter == null) {
					throw new IllegalStateException("Cannot apply reactive transaction to non-reactive return type: " +
						method.getReturnType());
				}
				return new ReactiveTransactionSupport(adapter);
			});
			return txSupport.invokeWithinTransaction(
				method, targetClass, invocation, txAttr, (ReactiveTransactionManager) tm);
			// 반응형 리턴 타입의 트랜잭션 메서드에 대한 Reactor 기반 관리
		}

		PlatformTransactionManager ptm = asPlatformTransactionManager(tm);
		final String joinpointIdentification = methodIdentification(method, targetClass, txAttr);

		if (txAttr == null || !(ptm instanceof CallbackPreferringPlatformTransactionManager)) {
			// getTransaction() 및 커밋/롤백 호출을 사용한 표준 트랜잭션
			TransactionInfo txInfo = createTransactionIfNecessary(ptm, txAttr, joinpointIdentification); // getTransaction() 호출

			Object retVal;
			try {
				// This is an around advice: Invoke the next interceptor in the chain.
				// This will normally result in a target object being invoked.
				retVal = invocation.proceedWithInvocation();
			}
			catch (Throwable ex) {
				// target invocation exception
				completeTransactionAfterThrowing(txInfo, ex); // rollback() 호출
				throw ex;
			}
			finally {
				cleanupTransactionInfo(txInfo);
			}
			...

			commitTransactionAfterReturning(txInfo); // commit() 호출
			return retVal;
		}
			...
		}
	}
	...
}
```
스프링 트랜잭션의 Aspect를 만드는 클래스다. invokeWithinTransaction()에서 호출하는 createTransactionIfNecessary()는 AbstractPlatformTransactionManager의 getTransaction()을 호출한다. 마찬가지로 completeTransactionAfterThrowing()에서 rollback()을 호출하고, commitTransactionAfterReturning()에서 commit()을 호출한다.

## 스프링의 트랜잭션 동작 방식
@Transactional이 붙은 클래스와 메서드는 어떤 방식으로 TransactionInterceptor을 거치게 될까? Spring Boot를 사용하는 경우, Auto Configuration에 의해 TransactionAutoConfiguration이 빈으로 등록된다.

### TransactionAutoConfiguration
```java
package org.springframework.boot.autoconfigure.transaction;

@Configuration(proxyBeanMethods = false)
@ConditionalOnClass(PlatformTransactionManager.class)
@AutoConfigureAfter({ JtaAutoConfiguration.class, HibernateJpaAutoConfiguration.class,
	DataSourceTransactionManagerAutoConfiguration.class, Neo4jDataAutoConfiguration.class })
@EnableConfigurationProperties(TransactionProperties.class)
public class TransactionAutoConfiguration {
	...
	@Configuration(proxyBeanMethods = false)
	@ConditionalOnBean(TransactionManager.class)
	@ConditionalOnMissingBean(AbstractTransactionManagementConfiguration.class)
	public static class EnableTransactionManagementConfiguration {

		@Configuration(proxyBeanMethods = false)
		@EnableTransactionManagement(proxyTargetClass = false)
		@ConditionalOnProperty(prefix = "spring.aop", name = "proxy-target-class", havingValue = "false")
		public static class JdkDynamicAutoProxyConfiguration {

		}

		@Configuration(proxyBeanMethods = false)
		@EnableTransactionManagement(proxyTargetClass = true)
		@ConditionalOnProperty(prefix = "spring.aop", name = "proxy-target-class", havingValue = "true",
			matchIfMissing = true)
		public static class CglibAutoProxyConfiguration {

		}

	}
}
```

이 클래스에는 트랜잭션을 위한 자동 설정이 들어있다. 그 중 EnableTransactionManagementConfiguration에 프록시와 관련된 Configuration 클래스가 있는 것을 확인할 수 있다. JdkDynamicAutoProxyConfiguration과 CglibAutoProxyConfiguration에 사용된 @EnableTransactionManagement 어노테이션 코드를 열어보자.

### @EnableTransactionManagement
```java
package org.springframework.transaction.annotation;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Import(TransactionManagementConfigurationSelector.class)
public @interface EnableTransactionManagement {

	boolean proxyTargetClass() default false;

	AdviceMode mode() default AdviceMode.PROXY;

	int order() default Ordered.LOWEST_PRECEDENCE;
}
```

TransactionManagementConfigurationSelector를 import하고 있다. 이 클래스도 열어보자.

### TransactionManagementConfigurationSelector
```java
package org.springframework.transaction.annotation;

public class TransactionManagementConfigurationSelector extends AdviceModeImportSelector<EnableTransactionManagement> {

	@Override
	protected String[] selectImports(AdviceMode adviceMode) {
		switch (adviceMode) {
			case PROXY: 
				return new String[] {AutoProxyRegistrar.class.getName(), 
					ProxyTransactionManagementConfiguration.class.getName()};
			case ASPECTJ:
				return new String[] {determineTransactionAspectClass()};
			default:
				return null;
		}
	}
	...
}
```
TransactionManagementConfigurationSelector는 @Configuration 클래스의 EnableTransactionManagement.mode 값을 기반으로 어떤 AbstractTransactionManagementConfiguration 구현을 사용해야 하는지 선택한다. 위를 올려 보면 default로 AdviceMode.PROXY인 것을 확인할 수 있다. 그래서 ProxyTransactionManagementConfiguration 설정이 로드된다. 이 클래스도 열어보자.

> **주의! PROXY 모드에서 다음의 경우 트랜잭션이 동작하지 않는다.**  
> - protected 혹은 private 메서드에 @Transactional이 붙은 경우 트랜잭션이 동작하지 않는다. public이 아닌 메서드에 적용하고 싶은 경우 AspectJ Mode를 고려해야 한다.
> - @Transactional이 적용되지 않은 public 메서드에서 @Transactional이 적용된 public 메서드를 호출하는 경우 트랜잭션이 동작하지 않는다.

### ProxyTransactionManagementConfiguration
```java
package org.springframework.transaction.annotation;

@Configuration(proxyBeanMethods = false)
@Role(BeanDefinition.ROLE_INFRASTRUCTURE)
public class ProxyTransactionManagementConfiguration extends AbstractTransactionManagementConfiguration {

	@Bean(name = TransactionManagementConfigUtils.TRANSACTION_ADVISOR_BEAN_NAME)
	@Role(BeanDefinition.ROLE_INFRASTRUCTURE)
	public BeanFactoryTransactionAttributeSourceAdvisor transactionAdvisor(
		TransactionAttributeSource transactionAttributeSource, TransactionInterceptor transactionInterceptor) {

		BeanFactoryTransactionAttributeSourceAdvisor advisor = new BeanFactoryTransactionAttributeSourceAdvisor();
		advisor.setTransactionAttributeSource(transactionAttributeSource);
		advisor.setAdvice(transactionInterceptor);
		if (this.enableTx != null) {
			advisor.setOrder(this.enableTx.<Integer>getNumber("order"));
		}
		return advisor;
	}

	@Bean
	@Role(BeanDefinition.ROLE_INFRASTRUCTURE)
	public TransactionAttributeSource transactionAttributeSource() {
		return new AnnotationTransactionAttributeSource();
	}

	@Bean
	@Role(BeanDefinition.ROLE_INFRASTRUCTURE)
	public TransactionInterceptor transactionInterceptor(TransactionAttributeSource transactionAttributeSource) {
		TransactionInterceptor interceptor = new TransactionInterceptor();
		interceptor.setTransactionAttributeSource(transactionAttributeSource);
		if (this.txManager != null) {
			interceptor.setTransactionManager(this.txManager);
		}
		return interceptor;
	}
}
```
ProxyTransactionManagementConfiguration는 프록시 기반 & 어노테이션 기반의 트랜잭션 관리를 활성화하는 데 필요한 스프링 인프라 빈을 등록하는 Configuration 클래스다. 트랜잭션의 경계를 담당하는 TransactionInterceptor를 설정하고, AnnotationTransactionAttributeSource를 만든다. 

### AnnotationTransactionAttributeSource
```java
package org.springframework.transaction.annotation;

public class AnnotationTransactionAttributeSource extends AbstractFallbackTransactionAttributeSource
		implements Serializable {
	...
	public AnnotationTransactionAttributeSource(boolean publicMethodsOnly) {
		this.publicMethodsOnly = publicMethodsOnly;
		if (jta12Present || ejb3Present) {
			this.annotationParsers = new LinkedHashSet<>(4);
			this.annotationParsers.add(new SpringTransactionAnnotationParser());
			if (jta12Present) {
				this.annotationParsers.add(new JtaTransactionAnnotationParser());
			}
			if (ejb3Present) {
				this.annotationParsers.add(new Ejb3TransactionAnnotationParser());
			}
		}
		else {
			this.annotationParsers = Collections.singleton(new SpringTransactionAnnotationParser());
		}
	}
	...
}
```
AnnotationTransactionAttributeSource는 이름에서 알 수 있듯이 @Transactional 어노테이션을 읽고 해당 트랜잭션 속성을 스프링의 트랜잭션 인프라로 전달해준다. 어노테이션을 파싱하기 위해 내부적으로 SpringTransactionAnnotationParser를 활용한다.

```java
package org.springframework.transaction.annotation;

public class SpringTransactionAnnotationParser implements TransactionAnnotationParser, Serializable {
	...
	protected TransactionAttribute parseTransactionAnnotation(AnnotationAttributes attributes) {
		RuleBasedTransactionAttribute rbta = new RuleBasedTransactionAttribute();

		Propagation propagation = attributes.getEnum("propagation");
		rbta.setPropagationBehavior(propagation.value());
		Isolation isolation = attributes.getEnum("isolation");
		rbta.setIsolationLevel(isolation.value());

		rbta.setTimeout(attributes.getNumber("timeout").intValue());
		String timeoutString = attributes.getString("timeoutString");
		Assert.isTrue(!StringUtils.hasText(timeoutString) || rbta.getTimeout() < 0,
			"Specify 'timeout' or 'timeoutString', not both");
		rbta.setTimeoutString(timeoutString);

		rbta.setReadOnly(attributes.getBoolean("readOnly"));
		rbta.setQualifier(attributes.getString("value"));
		rbta.setLabels(Arrays.asList(attributes.getStringArray("label")));

		List<RollbackRuleAttribute> rollbackRules = new ArrayList<>();
		for (Class<?> rbRule : attributes.getClassArray("rollbackFor")) {
			rollbackRules.add(new RollbackRuleAttribute(rbRule));
		}
		for (String rbRule : attributes.getStringArray("rollbackForClassName")) {
			rollbackRules.add(new RollbackRuleAttribute(rbRule));
		}
		for (Class<?> rbRule : attributes.getClassArray("noRollbackFor")) {
			rollbackRules.add(new NoRollbackRuleAttribute(rbRule));
		}
		for (String rbRule : attributes.getStringArray("noRollbackForClassName")) {
			rollbackRules.add(new NoRollbackRuleAttribute(rbRule));
		}
		rbta.setRollbackRules(rollbackRules);

		return rbta;
	}
	...
}
```
@Transactional 어노테이션에 적용된 어트리뷰트를 TransactionAttribute로 파싱해서 리턴한다.

### application.yml
```yml
logging:
  level:
    org.springframework.aop.framework: TRACE
    org.springframework.transaction: TRACE
```
트랜잭션 관련 로그를 보기 위해 AOP 패키지와 트랜잭션 패키지의 로그 레벨을 TRACE로 설정하자.

### log
```log
2022-04-09 13:24:54.262 TRACE 7053 --- [           main] t.a.AnnotationTransactionAttributeSource : Adding transactional method 'com.example.demo.service.TransactionService.transaction1' with attribute: PROPAGATION_REQUIRED,ISOLATION_DEFAULT
2022-04-09 13:24:54.267 TRACE 7053 --- [           main] o.s.aop.framework.CglibAopProxy          : Creating CGLIB proxy: SingletonTargetSource for target object [com.example.demo.service.TransactionService@117b2cc6]
2022-04-09 13:24:54.271 TRACE 7053 --- [           main] o.s.aop.framework.CglibAopProxy          : Unable to apply any optimizations to advised method: public void com.example.demo.service.TransactionService.transaction1()
2022-04-09 13:24:54.271 TRACE 7053 --- [           main] t.a.AnnotationTransactionAttributeSource : Adding transactional method 'com.example.demo.service.TransactionService.transaction2' with attribute: PROPAGATION_REQUIRED,ISOLATION_DEFAULT
2022-04-09 13:24:54.271 TRACE 7053 --- [           main] o.s.aop.framework.CglibAopProxy          : Unable to apply any optimizations to advised method: public void com.example.demo.service.TransactionService.transaction2()
2022-04-09 13:24:54.272 TRACE 7053 --- [           main] o.s.aop.framework.CglibAopProxy          : Found 'equals' method: public boolean java.lang.Object.equals(java.lang.Object)
2022-04-09 13:24:54.272 TRACE 7053 --- [           main] o.s.aop.framework.CglibAopProxy          : Unable to apply any optimizations to advised method: public java.lang.String java.lang.Object.toString()
2022-04-09 13:24:54.272 TRACE 7053 --- [           main] o.s.aop.framework.CglibAopProxy          : Found 'hashCode' method: public native int java.lang.Object.hashCode()

```
애플리케이션을 실행하면 위와 같은 로그를 확인할 수 있다. TransactionService의 transaction1(), transaction2() 메서드가 (@Transactional의 기본 값) PROPAGATION_REQUIRED, ISOLATION_DEFAULT인 트랜잭션 메서드로 추가되었다. 그리고 스프링 부트 2.0 이상을 사용하고 있으므로 CglibAopProxy을 통해 프록시 객체가 생성되는 것을 확인할 수 있다. CglibAopProxy 클래스는 스프링 AOP를 위한 CGLIB 기반의 AopProxy 구현체다. CglibAopProxy는 잠시 뒤에 확인해보자.  

트랜잭션 정보가 로깅된 클래스인 AnnotationTransactionAttributeSource는 아까 확인했던 클래스다. @Transactional 어노테이션을 읽고 해당 트랜잭션 속성을 스프링의 트랜잭션 인프라로 전달하는 역할을 했다. 그런데 AnnotationTransactionAttributeSource에는 로그를 출력하는 코드가 없다.

### AbstractFallbackTransactionAttributeSource
```java
public class AnnotationTransactionAttributeSource extends AbstractFallbackTransactionAttributeSource ...
```
로그가 실제로 출력되는 곳은 부모 클래스인 AbstractFallbackTransactionAttributeSource다.

```java
public abstract class AbstractFallbackTransactionAttributeSource 
		implements TransactionAttributeSource, EmbeddedValueResolverAware {
	...
	@Override
	@Nullable
	public TransactionAttribute getTransactionAttribute(Method method, @Nullable Class<?> targetClass) {
		...
			TransactionAttribute txAttr = computeTransactionAttribute(method, targetClass);
			...
				if (logger.isTraceEnabled()) {
					logger.trace("Adding transactional method '" + methodIdentification + "' with attribute: " + txAttr);
				}
				this.attributeCache.put(cacheKey, txAttr);
			}
			return txAttr;
		}
	}
	...
}
```
getTransactionAttribute() 메서드에서 로깅 코드를 확인할 수 있다. 이 메서드는 파라미터로 들어오는 Method의 호출에 대한 트랜잭션 속성을 판별한다. 메서드에 명시된 트랜잭션 속성이 없으면 기본적으로 클래스의 트랜잭션 속성을 사용한다. 이제 이 getTransactionAttribute() 메서드를 누가 호출하는지 확인해보면 크게 2개의 클래스를 발견할 수 있다.

- TransactionAspectSupport
- TransactionAttributeSourcePointcut

### TransactionAspectSupport
```java
package org.springframework.transaction.interceptor;

public abstract class TransactionAspectSupport implements BeanFactoryAware, InitializingBean {
	...
	@Nullable
	protected Object invokeWithinTransaction(Method method, @Nullable Class<?> targetClass, 
		final InvocationCallback invocation) throws Throwable {

		// TransactionAttribute가 null인 경우 이 메서드는 트랜잭션이 아니다.
		TransactionAttributeSource tas = getTransactionAttributeSource();
		final TransactionAttribute txAttr = (tas != null ? tas.getTransactionAttribute(method, targetClass) : null);
		final TransactionManager tm = determineTransactionManager(txAttr);
	...
}
```
이 쯤되면 기억이 안 나겠지만, TransactionAspectSupport 클래스도 이미 만난 적이 있다. 이 클래스는 다른 여러 템플릿 메서드에 위임하는 Around-Advice 기반의 하위 클래스에 대한 대리자 클래스다. TransactionAspectSupport가 있기 때문에 다양한 Aspect 시스템에서 스프링의 기본 트랜잭션 인프라를 이용해서 Aspect를 구현할 수 있는 것이다. 전략 디자인 패턴(Strategy Pattern)이 적용된 케이스다. 전략 디자인 패턴은 실행 중에 알고리즘을 선택할 수 있게 하는 디자인 패턴이다. 특정한 계열의 알고리즘들을 정의하고 각 알고리즘을 캡슐화하기 때문에 그 계열에 속한 여러 알고리즘들을 자유롭게 교체할 수 있는 것이 특징이다.

### TransactionAttributeSourcePointcut
```java
package org.springframework.transaction.interceptor;

abstract class TransactionAttributeSourcePointcut extends StaticMethodMatcherPointcut implements Serializable {
	...
	@Override
	public boolean matches(Method method, Class<?> targetClass) {
		TransactionAttributeSource tas = getTransactionAttributeSource();
		return (tas == null || tas.getTransactionAttribute(method, targetClass) != null);
	}
}
```
TransactionAttributeSourcePointcut은 이름에서 알 수 있듯이 AOP의 Pointcut이다. Pointcut은 Advice 메소드가 적용될 메소드를 골라준다. matches() 메서드의 파라미터 method에 Advice가 적용될 수 있는지 판별한다. 만약 method가 트랜잭션이 적용될 타겟이 맞다면 TransactionAttributeSource에 값이 존재할 것이고, 그렇지 않다면 null이 들어갈 것이다. 이제 matches()를 호출하는 코드를 찾아보자. 

## 참고
https://mangkyu.tistory.com/169  
https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/transaction/annotation/Transactional.html  
https://sup2is.github.io/2021/11/11/about-spring-transaction.html  
https://hwannny.tistory.com/98  
https://ko.wikipedia.org/wiki/%EC%A0%84%EB%9E%B5_%ED%8C%A8%ED%84%B4  