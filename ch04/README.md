# 04 인덱스

인덱스(색인) 란 자료를 쉽고 빠르게 찾을 수 있도록 만든 데이터 구조이다. 데이터베이스에서의 인덱스는 데이터를 빨리 찾기 위해 투플의 키 값에 대한 물리적 위치를 기록해둔 자료 구조이다. 일반적인 RDBMS 의 인덱스는 대부분 B-tree 구조로 되어 있다. 

#### + B-tree 에 대해서
```
B-tree 는 상위부터 루트 노드, 내부 노드, 리프 노드로 나뉘어진다. 모든 검색은 최상위 루트 노드부터 시작해서 내부 노드를 지나
리프 노드까지 내려가면서 이루어진다.

B-tree 의 노드에 값이 추가될 경우 루트 노드에서부터 시작해서 값을 비교하면서 삽입될 위치를 찾는다. 노드에 저장할 공간이 없으면
새로운 노드로 분할하여 값을 이동시킨 후 삽입한다. 값이 새로 입력되어도 트리는 항상 균형 상태를 유지한다.

B-tree 의 각 노드는 키 값과 포인터를 가지며 키 값은 오름차순으로 저장되고 키 값 좌우에 있는 포인터는 각각 키 값보다 작은 값과
큰 값을 가진 다음 노드를 가리킨다.

예를 들어 3 이라는 키 값을 찾고 싶으면 루트노드 부터 시작해서 3 이 루트 노드의 키 값보다 작은 값일 경우 왼쪽으로 이동하고
큰 값일 경우 오른쪽으로 이동해서 값을 찾아나간다.

인덱스는 테이블에서 한 개 이상의 속성을 이용하여 생성한다. 순서대로 정렬된 속성과 데이터의 위치만 보유하므로 테이블보다
작은 공간을 차지하는 장점이 있다. (검색 성능 향상) 
```
## MySQL 의 인덱스

MySQL 인덱스는 클러스터 인덱스와 보조 인덱스로 나뉘어지며 모두 B-tree 인덱스를 기본으로 한다.

### 1. 클러스터 인덱스
```
클러스터 인덱스는 루트 노드의 키(PK 등) 값과 정렬된 리프 노드의 위치 값을 가진다. 리프 노드에는 루트 노드에 정렬된 순서대로
'테이블 데이터 자체'가 저장된다. 248 p 

클러스터 인덱스를 사용하면 이미 정렬된 데이터의 위치를 가지고 있기 때문에 특정 값을 쉽게 찾을 수 있으며 인덱스 페이지가 단순해서
인덱스 저장 시 차지하는 공간도 작다.

클러스터 인덱스는 키 값에 의한 동등 및 범위(BETWEEN) 검색 모두에 유리하다. 

MySQL 에서 클러스터 인덱스는 테이블 생성시 기본키(PK) 를 생성하면 자동으로 생성된다.
기본키를 지정하지 않으면 먼저 나오는 UNIQUE 속성에 대해 클러스터 인덱스를 생성한다.

기본키나 UNIQUE 속성이 없는 테이블은 MySQL 이 자체 생성한 행 번호(Row ID) 를 이용해서 클러스터 인덱스를 생성한다.
```
### 2. 보조 인덱스 
```
데이터의 변경이 일어나면 처음 저장된 순서대로 데이터를 유지할 수 없다. 아이디 값으로 숫자를 사용하면 아이디 값의 순서가 바뀐다던지
새로운 아이디가 생겨서 순서 자체가 섞이게 된다.

보조 인덱스를 사용하면 인덱스 키로 설정된 값으로 루트 노드의 범위를 나누고 리프 노드에서는 키 값이 위치하는 테이블의 위치 값을 가지고
데이터를 찾는다.

보조 인덱스는 테이블 당 여러 개를 만들 수 있다. 컬럼 하나만을 대상으로 할 수도 있고 컬럼을 복합적으로 결합해서 만들 수도 있다.
예를 들면 출판사 이름과 가격 컬럼을 인덱스로 생성하면 '어떤 출판사의 얼마짜리' 책을 찾는 등과 같은 업무에 빠르게 대응할 수 있다. 250 p

보조 인덱스를 사용할 때 유의해야할 점은 특정 키 값을 찾는 검색은 위치 값을 사용해서 쉽게 찾을 수 있지만, 데이터의 범위를 검색할 때는
위치 값이 섞여 있기 때문에 빠른 검색 효과를 보장할 수 없다.
```
### 3. MySQL 에서 인덱스 사용하기 
```
앞서 설명했듯이 인덱스를 구성할 때는 자료의 저장 및 질의 형태에 따라 신중하게 생성해야 한다.

클러스터 인덱스와 보조 인덱스는 보통 같이 사용되는데 Book 테이블에서 범위 검색을 할 수 있는 bookid(숫자) 를 클러스터 인덱스로 만들고
(PK 값을 보통 인위적으로 만들어서 사용학 때문에 인위적인 데이터의 아이디 값이 될 것이다.) bookname(문자) 를 보조 인덱스로 구성할 수 있다. 

MySQL 은 bookid 를 이용해서 검색할 때는 클러스터 인덱스를 사용해서 범위 내에서 빠르게 데이터를 조회할 수 있고 bookname 으로 데이터를
검색하면 보조 인덱스를 사용해서 책의 아이디 값을 리프 노드에서 찾은 다음 아이디 값을 클러스터 인덱스를 사용해서 검색해올수 있다. 250 p

MySQL 이 인덱스를 이렇게 하는 이유는 클러스터 인덱스로 저장된 데이터의 순서를 가능한 유지하면서 데이터의 삽입과 삭제에 대한 인덱스 관리
비용을 줄일 수 있기 때문이다. 
```
## 인덱스 생성하기 
```
인덱스를 생성한다고 해서 무조건 검색이 빨라지지는 않는다. 선택도가 높을 경우 인덱스가 없는 게 더 빠를 수 있다.
선택도란 '1/서로 다른 값의 개수' 을 의미한다. 선택도가 높을 경우 인덱스가 없는 게 더 빠를 수 있다.

예를 들어 100 개의 행을 가진 테이블에 값이 (남,여) 두 가지라면 선택도가 높다고 할 수 있다.
```
#### + 인덱스 생성시 고려 사항
```
- 인덱스는 WHERE 절에 자주 사용되는 속성이여야 한다.
- 인덱스는 조인에 자주 사용되는 속성이어야 한다.
- 단일 테이블에 인덱스가 많으면 속도가 느려질 수 있다(테이블당 4 ~ 5 개의 인덱스를 만들자)
- 속성이 가공되는 경우 사용하지 않는다?
- 속성의 선택도가 낮을 때 유리하다(속성의 모든 값이 다른 경우)

CREATE [UNIQUE] INDEXT[인덱스 이름]
ON 테이블 이름 (컬럼 [ACS | DECS] [{, 컬럼 [ACS | DECS]} ...])[;]

여기서 UNIQUE 는 테이블의 속성 값에 대하여 중복이 없는 유일한 인덱스를 생성하는 것을 의미한다. 
```
### 인덱스 생성 예시
```
ex) Book 테이블의 bookname 열을 대상으로 인덱스 ix_Book 생성하기
CREATE INDEX ix_Book ON Book(bookname);

ex) Book 테이블의 publisher, price 열을 대상으로 인덱스 ix_Book2 생성하기
CREATE INDEX ix_Book2 ON Book(publisher, price);

SHOW INDEX FROM Book; // 인덱스 확인 명령어

MySQL 이 생성된 인덱스를 활용하는 것을 확인하려면 [Query] -> [Explain Current Statemnet] 를 누르면 된다.

SELECT * FROM Book
WHERE publisher='대한미디어' AND price >= 30000;
```
## 인덱스의 재구성과 삭제
```
B-tree 인덱스는 데이터의 변경이 잦으면 노드의 갱신이 주기적으로 일어나 삭제된 레코드의 인덱스 값 자리가 비게되는
단편화 현상이 발생하는데 이는 검색시 성능 저하로 이어진다.

ANALYZE TABLE 테이블이름; // 이 커맨드로 인덱스를 다시 생성하면 최적화 할 수 있다.
DROP INDEX ix_Book ON Book; // 인덱스 삭제 커맨드 
```


