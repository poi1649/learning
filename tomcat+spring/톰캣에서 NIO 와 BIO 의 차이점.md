
## BIO 

- 새로운 커넥션이 체결(accept)되면  항상 새로운 쓰레드를 할당한다.
- http keep-alive 시간을 설정하게 됐을 때, 실질적으로 커넥션을 통해 데이터를 주고받고 있지 않을 때도 쓰레드가 점유될 수 있다.

> 참고로 tomcat 9.0 부터 BIO는 deprecate 되었다.

### NIO 

- 새로운 커넥션은 항상 `Poller`로 전달된다.
- `Poller`는 커넥션에 처리될 수 있는 데이터가 있을 때 이벤트를 전달받고, 이 때 해당 커넥션에 쓰레드를 할당해주고, 데이터의 읽기 쓰기가 끝나면 다시 `Poller`로 해당 커넥션을 전달한다.

- `Poller` 는 `Selector` 기반의 멀티플렉싱 방식을 사용한다. 
- `Poller` 쓰레드가 `Selector를` 가지고 여러개의 채널 (여기서는 커넥션이라고 할 수 있을 것 같다.)에 대한 이벤트를  관리한다. 만약 어떤 채널에 이벤트가 발생하면 쓰레드를 할당해주는 방식이다.


- 톰캣에서 사용되는 NIO Connector는 여러가지 있는데 주요 차이점은 주로 SSL에만 존재한다.

![[/sources/Pasted image 20230918230803.png]]

출처: https://tomcat.apache.org/tomcat-8.5-doc/config/http.html#Connector_Comparison

