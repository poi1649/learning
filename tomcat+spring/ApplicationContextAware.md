
- 스프링의 `ApplicationContext` 는 어플리케이션 안의 `Bean` 을 싱글턴(기본 스코프라고 가정)으로 가지고 있다.

- 어떤 `Bean` 이 다른 `Bean` 에 의존을 가진다면 이를 주입해주기도 한다.

- `ViewResolver`, `HandlerMapping` 처럼 여러 `Bean` 의 정보를 전부 검사하고 이를 필터링해서 리스트로 만들어햐 하는 경우도 있는데 이런 경우 `ApplicationContext`에게 `get` 쿼리를 날려서 `Bean` 을 가져오고 필터링한다.

- 이런 경우 물론 `ApplicationContext` 가 `ViewResolver`, `HandlerMapping`도 가지고 있을 것인데, 이들 또한 `ApplicationContext `를 알게되는 사실상 양방향 참조가 일어난다.

- 이와 같이 `ApplicationContext에` 의해 생성되지만 `ApplicationContext를` 알기도 해야하는 특수한 `Bean` 들은 `ApplicationContextAware라는` 인터페이스를 구현하게 된다.

- 이 인터페이스는 `setApplicationContext(ApplicationContext ctxt)` 라는 메서드를 가지며, 이의 구현체들이 `ApplicationContext` 를 필드로 가지고 있다.