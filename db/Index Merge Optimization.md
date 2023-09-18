
MySQL 에서는 Index Merge Optimization 이라는 기능을 지원한다.

이는 시도하려는 쿼리의 `WHERE` 절에 인덱스가 여러 개일 경우, 해당 인덱스에 대해 동시에` Index range scan` 을 진행하고, 이 결과의 행들을 합집합(UNION) 또는 교집합(INTERSECTION) 형태로 만들어 반환하는 최적화 기능이다.

```sql

SELECT * FROM _tbl_name_ WHERE _key1_ = 10 OR _key2_ = 20; 

SELECT * FROM _tbl_name_ WHERE _key1_ = 10 AND _key2_ = 20
```

간단한 예시를 들어보자면, 위의 두 쿼리에서는 _key1_ 인덱스와 _key2_ 인덱스에 대해 별도의 레인지 스캔을 진행한다.
이 결과로 나온 행들을 1번 쿼리에서는 UNION(OR 이므로), 2번 쿼리에서는 INTERSECTION(AND 이므로) 하여 반환한다고 생각할 수 있다.


