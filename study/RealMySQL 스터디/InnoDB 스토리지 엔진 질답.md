
- 버퍼 풀이 뭔가요?
    - innoDB 스토리지 엔진의 특정한 메모리 공간. 데이터 페이지를 캐싱해서 보관
- 버퍼풀에서 캐싱되는 데이터는 무엇이 있나요?
    - 읽기 버퍼, 쓰기 버퍼
- 인서트 버퍼는 실제 데이터가 아니라 인덱스에 관련한 부분이다. 라는 말이 무슨 말인가요?
    - 전체 페이지가 아니고 변경된 인덱스만 기록한다는 뜻. 실제 변경될 데이터는 리두로그에 존재한다.
- ‘커밋이 된다고 해서 언두 영역이 바로 삭제되는 것은 아니다.’ 가 의미하는 바를 격리 수준과 연관 지어 설명해 주세요.
    - 하나의 레코드를 수정하는 여러 트랜잭션과 관계 있는 것이다. 격리 수준은 언두 로그를 읽는 상황에서 관련된 내용이다.
    - REPEATABLE READ와 관련해서 생각해보기
- 언두 로그에는 변경사항만 저장되는데 언두 로그만 읽는 것과 버퍼풀만 읽는 것과 성능 차이가 있을까?
    - 성능 차이가 없다고 보면 된다.
- 외래키 제약을 풀 수 있다. 외래키 제약 풀고 부모 삭제한 후 외래키 제약 다시 걸면 예외가 발생할까?
    - 안 생긴다. 아마 제약 조건을 삭제할 때만 확인하는 것 같다.
- 버퍼풀에서는 쓰기 폭파 현상이 발생할 수 있는데, 이가 어떻게 발생하는지 더티페이지와 관련해 설명해주세요
    - 더티 페이지가 있는 이유는 쓰기지연 때문이다, 이를 얻기 위해 버퍼풀에 더티페이지가 많아진다. 이 비율이 임계값을 넣어가면 플러시해야 한다는 판단을 스토리지 엔진이 하게되는데 이 때 더티 페이지가 생성되는 속도가 이노디비의 플러시 정책에서 감당할 수 있는 수준보다 빠르다면, 이런 현상이 발생한다.
    - 해결 방법은?
        - 어답티브 플러시 정책을 통해 버퍼풀의 더티페이지 비율을 조정한다.
- 버퍼 풀은 새로 들어온 페이지에 대해서 특정 자료구조를 통해 관리한다. 이때 자료구조에 데이터를 넣는 전략으로 LRU와 MRU를 취하고 있다. 새로운 데이터가 들어올 때, 왜 LRU의 끝이 아니라 중간에 넣게 될까?
    - 하나의 페이지를 1,3,5 번째 순서로 조회한다고 할 때, LRU의 헤드에 들어오게 되면 리스트에 삽입 됐다가 사라졌다가를 반복하게 된다. 자주 조회하는 페이지임에도 이러한 현상이 발생한다면 합리적이지 않을 것이라고 생각한다.
    - 왜 MRU의 헤드에 올라가지 않는 것인가? → 첫 번째 들어갈 때는 중간에 들어가지만, 그 후에 호출되면 MRU의 헤드로 바로 가게 된다. 한 단계씩 격상되는 게 아니다.
- 언두 로그의 역할?
    - mysql 서버 비정상 종료 되었을 때 롤백을 할 때 데이터 복구를 위해 사용된다. 또한 MVCC로 사용된다.
- 체인지 버퍼의 역할
    - 인덱스 값의 변경을 저장하는 버퍼
- 유니크 인덱스 → 체인지 버퍼 사용 불가능 이유?
    - 제약 조건에 맞는지 확인 과정이 반드시 필요
- REPEATABLE READ에서 어떤 매커니즘으로 데이터를 select 하나요?(언두로그 관련)
    - SELECT 쿼리 실행할 때 언두 로그를 참고하여 임시 테이블을 만든다. 그리고 REPEATBLE READ는 해당 임시 테이블을 읽는다.
    - 커밋된 트랜잭션 id를 통해 언두로그를 타고 올라가서 읽고자 하는 레코드가 존재하면 해당 언두로그를 임시 테이블로 만든다.
- 언두 로그 중에서 MVCC에 사용되지 않는 언두로그를 만드는 쿼리는 ? 언두 슬롯과 동시 트랜잭션에 대한 관계?
    - insert 명령이다. insert는 롤백 만을 위해 존재한다.
    - 트랜잭션이 많으면 언두 슬롯이 다 차고 언두 테이블 스페이스에 더 이상 가용공간이 없으면 트랜잭션 시작 불가능.
- 언두 테이블 스페이스 truncate 에 대해 설명해주세요
    - 더 이상 사용되지 않는 언두 로그들을 삭제해주는 명령
    - 이렇게 삭제된 공간은 운영체제로 반납됨
    - 퍼지 스레드가 자동으로 수행하지만, 언두 스페이스 테이블을 비활성화해서 수동으로도 가능
- 리두 로그는 어떤 식으로 체크포인트를 정하여 장애를 복구할 수 있도록 할까요?
    - 자동으로는 트랜잭션이 끝날 때마다 체크포인트를 정한다. 설정을 통해서 1초마다 체크포인트를 정할 수도 있다.
- 트랜잭션이 완료되고 파일에 작성하는 걸까요? 또는 로그를 먼저 작성할까요?
    - 로그를 먼저 작성한다
- 이것도 결국 IO 작업이고, 부하가 발생하는데 리두 로그를 통해 어떻게 성능을 향상시킬 수 있을까요?
    - 변경 사항만 기록하기 때문에 부하가 적을 것 같다.
- 어댑티브 해시 인덱스가 무엇일까요? 단점은 뭐고 장점은 뭐죠?
    - 버퍼풀 내에서 인덱싱을 하는 자료구조이다. 데이터 페이지 자체를 인덱스로 저장한다. id는 BTREE의 인덱스와 트리 인덱스의 실제 키값으로 구성된다.
    - 디스크 읽기가 많은 경우, 조인이나 LIKE 패턴 검색, 매우 큰 데이터를 가진 경우
    - 동등 조건 검색, 디스크의 데이터가 버퍼 풀 크기와 비슷한 경우