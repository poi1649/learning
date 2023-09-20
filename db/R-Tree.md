
- R-Tree 는 2차원의 정보를 이용해 공간의 개념을 인덱싱하기 위해 사용된다.

- MBR(Minimum Bounding Rectangle) 이라는 개념으로, 어떤 2차원의 점, 도형을 제일 작은 크기의 사각형으로 감싸고 이 사각형간의 포함관계를 통해 트리를 구성한다.

- `ST_Contains()`와 `ST_Within()` 을 통해 각 공간의 포함 관계를 쉽게 쿼리할 수 있다.

- `ST_Distance_Sphere()` 을 통해 더 정확한 거리도 계산할 수 있다.

