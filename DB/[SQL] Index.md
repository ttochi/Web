# INDEX
https://lalwr.blogspot.kr/2016/02/db-index.html

## INDEX란?
RDBMS에서 테이블에 저장된 데이터를 빠르게 조회하기 위한 데이터베이스 객체
INDEX는 `B-Tree` 구조로 색인화 된다.

### 원리
DB에서 table을 생성할 때 `MYD`, `MYI`, `FRM` 파일이 함께 만들어진다.
INDEX가 table의 column에 주게 되면 `MYI`에 이 정보가 저장된다.
사용자가 INDEX를 사용하는 쿼리를 사용하게 되면 table을 검색하는 게 아니라 tree구조로 정리된 `MYI` 파일을 검색한다.
(INDEX를 사용하지 않았다면 table을 full scan)

### 장점
- 키 값을 기초로 하여 테이블에서 검색과 정렬 속도를 향상시킵니다.
- 테이블의 기본 키는 자동으로 인덱스 됩니다.

### 단점
- INDEX는 논리적/물리적으로 테이블과 독립적임
- 레코드가 삽입, 수정, 삭제될 때 인덱스가 변경되어야 해서 성능이 떨어진다.
- 인덱스가 데이터베이스 공간을 차지해 추가적인 공간이 필요해진다. (DB의 10퍼센트 내외의 공간이 추가로 필요)

## INDEX의 생성
SELECT 쿼리의 WHERE절이나 JOIN 예약어를 사용했을때만 인덱스를 사용되며 SELECT 쿼리의 검색 속도를 빠르게 하는데 목적을 두고 있습니다.

> DELETE,INSERT,UPDATE 쿼리에는 오히려 INDEX 사용 시 좀 느려진다.

### 생성 지침
자동생성 : PK나 Unique제약 조건을 정의할 경우 Unique Index가 자동으로 생성됨

 - where절이나 join 조건에 자주 사용되는 column
 - column값이 unique한 경우
 - foregin key로 사용되는 column
 - update가 자주 발생하지 않는 column
 - 데이터의 중복도가 높은 열은 인덱스로 만들어도 효용이 없다. 
    - ex) 성별과 같이 타입이 별로 없는 경우

### Syntex
```
CREATE [UNIQUE] INDEX index_name ON table_name(column1[,column2]);
DROP INDEX index_name
```

**mysql에서 정보 확인**
```mysql
show INDEX from table_name
```
