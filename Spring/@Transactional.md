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

PlatformTransactionManager에 선언되었던 getTransaction(), commit(), rollback()을 구현하고 있다. commit()과 rollback()의 내부에는 processCommit(), processRollback()으로 커밋과 롤백을 처리한다. 그런데 processCommit()과 processRollback() 내부에서 추상 메서드인 doCommit(), doRollback()을 사용하고 있는 것을 볼 수 있다. 다양한 트랜잭션 매니저의 구현체(DataSourceTransactionManager, JpaTransactionManager, JtaTransactionManager 등)에서 이 doCommit()과 doRollback()을 구현하고 있다. 이렇게 구현체 별로 다른 동작을 하게 만드는 디자인 패턴을 템플릿 메서드 패턴이라 한다.

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
스프링은 AOP Proxy를 통해 트랜잭션을 처리한다. AOP는 여러 코드에 존재하는 공통된 부가기능 코드를 독립적으로 분리해주는 프로그래밍 기법이다. AOP는 JDK Dynamic Proxy와 CGLib 방식이 있다. 기본적으로 타겟 메서드가 시작되기 전에 트랜잭션을 시작하고 메서드가 정상적으로 종료되면 커밋하고 실패하면 롤백한다.

### JDK Dynamic Proxy
타겟이 인터페이스를 상속하는 경우 사용하는 방법이다. Java의 Reflection 패키지의 Proxy 클래스를 통해 프록시 객체를 생성한다. Dynamic Proxy 객체는 타겟이 상속하고 있는 인터페이스를 상속한 다음 추상메서드를 구현해서 내부적으로 타겟 메서드 호출 전 후에 트랜잭션 처리를 수행한다. 프록시 객체를 런타임 시점에 동적으로 만들기 때문에 Dynamic Proxy라 한다. 

### CGLib
타겟이 인터페이스를 상속하지 않는 경우 사용하는 방법이다.

## 참고
https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/transaction/annotation/Transactional.html  
https://sup2is.github.io/2021/11/11/about-spring-transaction.html  