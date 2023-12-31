
트랜잭션은 크게 **글로벌 트랜잭션**과 **로컬 트랜잭션**으로 나눌 수 있습니다.

### 글로벌 트랜잭션
글로벌 트랜잭션은 트랜잭션 자원(ex.데이터 소스)들이 여러개일때(ex. rdbms여러개와 메시지큐를 사용하는 경우), 이를 하나의 트랜잭션처럼 관리할 수 있게 만들어줍니다.

각 어플리케이션 서버들은 JTA 라는 표준 API 를 이용하여 트랜잭션을 관리하게 되는데, JTA의 유저 트랜잭션은  또 JNDI 라는 인터페이스를 이용하여 외부의 자원, 다른 프로그램을 찾게 됩니다.

> JTA는 Java Transaction API 의 준말로, XA 리소스 (분산 리소스) 간의 트랜잭션을 처리하는 API 입니다.

즉, 글로벌 트랜잭션을 이용하려면 JTA, JNDI 를 이해하고 사용해야만 합니다. 이 때문에, 학습 리소스가 추가로 소요되고, 이를 위한 서버가 추가적으로 필요하다는 단점이 있습니다.

### 로컬 트랜잭션
하나의 트랜잭션 자원을 이용할 때 사용됩니. 구현은 굉장히 쉽지만, 이렇게 작성된 코드는 글로벌 (JTA)트랜잭션에서 사용되지 못한다는 큰 단점이 있습니다. (즉, 자원이 스케일링 되면 코드를 재사용 할 수 없습니다.)
추가로, 트랜잭션 코드가 비즈니스 코드에 침투하게 된다는 단점도 있습니다.

> 이 부분에서 설명하는 로컬 트랜잭션의 단점은 스프링의 트랜잭션 기능을 사용하지 않는 경우에 국한합니다!


### TransactionManager
예전에는 트랜잭션 자원이 하나인 작은 서버에서는 로컬 트랜잭션을 코드로 구현했는데, 이는 굳이 JTA와 이를 위한 EJB 서버를 별도로 사용하지 않기 위해서라고 합니다.
추후 서버가 커질경우 코드단의 막대한 수정이 필요했는데, 스프링은 이를 추상화로 해결하였습니다.

대표적인 추상화된 클래스로 **PlatformTransactionManager** 와 **ReactiveTransactionManager**가 있는데, 이번 글에서는 PlatformTransactionManager 만 살펴보겠습니다.

```java
public interface PlatformTransactionManager extends TransactionManager {

	TransactionStatus getTransaction(TransactionDefinition definition) throws TransactionException;

	void commit(TransactionStatus status) throws TransactionException;

	void rollback(TransactionStatus status) throws TransactionException;
}
```

스프링의 PlatformTransactionManager 인터페이스 코드는 다음과 같습니다.
인터페이스기 때문에 어떤 구현에 강제되어 있지 않습니다. 
즉, 구현에따라 위에서 말한 JNDI로 트랜잭션 자원을 가져올 수도 있고, 로컬에서 가져올 수도 있습니다.
테스트도 훨신 쉬워지겠죠?

스프링답게 각 메서드가 던지는 예외도 uncheckedException 이기 때문에 사용자가 예외처리를 강제로 할  필요도 없습니다.

이 메서드 중, 첫 메서드 **getTransaction**을 살펴보겠습니다.

메서드의 파라미터로 전달되는 **TransactionDefinition** 은 우리가 잘 아는 4가지를 나타냅니다.
1. 트랜잭션의 전파 범위를 나타내는 **Propagation** , ex. requires_new
2. 트랜잭션의 고립 레벨을 나타내는 **Isolation** , ex. read umcommited
3. 트랜잭션이 롤백되는 한계 시간을 나타내는 **Timeout**
4. 트랜잭션이 자원을 읽기만 하는지 여부를 나타내는 **Read**-**only**

이 메서드는 **TransactionStatus** 를 반환합니다. 이 객체는 얻어올 수 있는 트랜잭션이 새로운 트랜잭션인지 또는 콜스택에 이미 존재하는 트랜잭션인지(즉, 쓰레드와도 연관이 있다고 볼 수 있습니다.) 등을 나타냅니다.
**TransactionStatus** 인터페이스의 상세 코드는 다음과 같습니다.
```java
public interface TransactionStatus extends TransactionExecution, SavepointManager, Flushable {

	@Override
	boolean isNewTransaction();

	boolean hasSavepoint();

	@Override
	void setRollbackOnly();

	@Override
	boolean isRollbackOnly();

	void flush();

	@Override
	boolean isCompleted();
}
```


### TransactionManger 선택
스프링에서 트랜잭션을 사용하려면 반드시 트랜잭션 매니저의 구현을 결정해야합니다. (우리가 잘아는 @Transaction 어노테이션을 사용하여 선언적으로 트랜잭션을 사용해도 마찬가지입니다.)

xml 을 쓰면 요런식으로 dataSource와 트랜잭션 매니저를 정해줄 수 있습니다.

```xml
<bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="dataSource"/>
</bean>
```

> 여기서 쓰인 DataSourceTransactionManager는 대표적인 로컬 트랜잭션 매니저 입니다! 우리가 JDBC Template을 사용하는 어플리케이션을 작성하면 일반적으로 해당 트랜잭션 매니저를 사용하게 됩니다.

반대로 글로벌 트랜잭션을 사용한다면 다음과 같이 xml을 작성할 수 있습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:jee="http://www.springframework.org/schema/jee"
	xsi:schemaLocation="
		http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/jee
		https://www.springframework.org/schema/jee/spring-jee.xsd">

	<jee:jndi-lookup id="dataSource" jndi-name="jdbc/jpetstore"/>

	<bean id="txManager" class="org.springframework.transaction.jta.JtaTransactionManager" />

	<!-- other <bean/> definitions here -->

</beans>
```

이 코드는 Jakarta EE 컨테이너안에서 JTA를 사용할 때 필요한 코드인데, 위에서 말한대로 dataSource를 jdni-lookup으로 얻어오는 것을 볼 수 있습니다.


이렇게 TransactionManager와 관련된 몇몇 빈을 갈아 끼우는 것만으로 로컬 / 글로벌 트랜잭션의 선택을 자유롭게 할 수 있습니다. 

### 정리

결국 이 또한 **추상화**로 책임을 분리하고, 변경을 최소화했다 라고 볼 수 있습니다.
스프링에 대해 공부하다보면 항상 결론은 똑같은 것 같은데, 아마 스프링의 철학과 관련이 있겠죠?🤔



