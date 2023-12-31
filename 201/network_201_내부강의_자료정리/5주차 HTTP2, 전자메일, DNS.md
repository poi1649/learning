
#### HTTP/2
HTTP/1.0 에서는 브라우저들이 웹 페이지 하나당 하나의 연결만을 서버와 체결할 수 있었다.
이 때문에 여러 요청에 대해 응답을 받아올 때, 너무 큰 사이즈의 객체 응답이 전송될 때, 다른 객체들의 응답까지 지연되어버리는 HOL Blocking 문제가 발생했다.
이를 HTTP/1.1 에서는 웹페이지 하나에 여러개의 병렬 TCP 연결을 열어 동시에 응답을 받아오는 방식으로 해결했는데, 이는 TCP 혼잡 제어에 일종의 속임수를 쓰는 격이다.

HTTP/2 에서는 HOL Blocking도 해결하고, TCP 혼잡 제어도 잘 작동할 수 있도록 프레이밍 기법을 도입하게 된다.
하나의 메시지를 프레임으로 나누어 같은 TCP 연결에서 인터리빙 하는 방식이다.
이 프레이밍은 HTTP의 프레이밍 서브 계층에 의해 이루어지고, 헤더는 하나의 프레임, 본문은 여러개의 프레임으로 쪼개 전송하게 된다.
프레이밍 계층인 이뿐만 아니라 프레임을 바이너리로 인코딩에 파싱의 효율을 높였으며, 에러에 강건하게 만들었다.

또, 각 메시지에 우선순위를 부여하여 우선순위가 높은 메시지의 프레임은 먼저 전송될 수 있게끔 하게도 하였다.

이 외에도 HTTP/2 에서는 서버 푸쉬라는 기능도 도입하였는데, 이는 객체들의 요청이 도착하기 전에 필요한 객체들을 클라이언트에 미리 보내는 기능이다. 요청의 추가 지연 시간을 없앨 수 있다.

#### 전자메일
메일 시스템은 메일 에이전트, 메일 서버, SMTP로 구성된다.

메일 에이전트는 우리가 사용하는 Gmail앱, 마이크로소프트 아웃룩 등이 있다.
메일 서버는 클라이언트와 서버 역할을 둘 다 가진다. 송신자의 메일을 수신해 수신자의 메일 서버에 전달하고, 수신자의 메일 서버는 메일을 메일 박스에 저장한다.

메일 에이전트는 먼저 메일 서버에 자신의 메시지를 보내고, 메일 서버는 해당 메시지를 메시지 큐에 놓는다.
메일 서버의 SMTP 클라이언트는 메시지큐를 살피고, 순서대로 꺼내 전송한다.
이 전송과정은 TCP 위에서 이루어지고, TCP 연결 체결 후 각 메시지마다 SMTP 핸드 셰이크도 이루어진다.

메일 수신 서버는 데이터 .을 수신하면 전부 수신했다고 받아들인다.

수신자의 메일 에이전트는 자신의 메일 서버의 메일박스를 확인하기 위해 SMTP가 아닌 HTTP, IMAP, POP3 등의 프로토콜을 사용한다.

#### DNS
DNS는 인터넷상의 호스트 네임과 IP를 매핑해주는 계층형 시스템이다.
추가로 다음과 같은 기능도 제공한다.

호스트 앨리어싱
호스트가 여러 이름을 가질 수 있다. 하나의 IP에 여러가지 도메인 네임을 매핑한다.

메일 앨리어싱
메일 서버가 길고 복잡해도, 일반적인 서버 이름으로 별명을 매핑할 수 있다.

부하 분산
하나의 호스트에 여러개의 IP를 매핑해 서버의 부하를 분산할 수도 있다.

DNS가 중앙 집중형이 아닌 이유는 다음과 같다.
SPOF
트래픽이 쏠림
사용자와 멈
레코드에 대한 관리

DNS는 계층형의 구조를 갖는다.
먼저 Root 레벨의 DNS는 각 TLD(Top Level Domain) DNS 서버의 정보를 갖는다.
각 TLD들은 여러개의 책임 DNS 서버에 대한 IP주소를 제공한다.
책임 DNS 서버들은 인터넷에서 접근하게 되는 호스트의 DNS레코드를 가진다.

대부분의 호스트들은 위 계층보다 한단계 아래라고 할 수 있는 로컬 DNS서버를 가진다.
이 로컬 DNS 서버들은 각각의 LAN 또는 Router 단위로 존재한다고 생각해도 무방하다.

각각의 호스트들의 요청은 가까운 로컬 DNS서버로 먼저 이관되고, 로컬 DNS가 이를 반복적으로 질의해 ip를 알아낸 후 다시 호스트에게 전달해준다.

각각의 로컬 DNS들은 이렇게 얻어온 레코드를 캐싱한다.
시간이 지나면 레코드들은 파기되지만, 보통 TLD의 정보까지는 대부분 캐싱이 되어있다.

DNS의 레코드 타입은 네가지로 나뉘어진다.
A -> 도메인 이름을 key, ip를 value로 가진다.
CNAME -> 도메인 별명을 key, 도메인 진명을 value로 가진다.
MX -> 도메인 이름을 key, 메일서버이름을 value로 가진다.
NS -> 도메인 이름을 key, 도메인서버 이름을 value로 가진다.


