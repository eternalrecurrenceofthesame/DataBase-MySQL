# 정규화

잘못 설계된 테이블로 삽입, 삭제, 수정 작업을 하면 조회시 이상현상이 발생한다. 이상현상은 데이터의 일관성을 훼손하고 데이터의 무결성을 깨뜨리는데 이를 정상으로 만드는 과정을 정규화라고 한다. 

# 01 이상현상

### 이상현상의 개념
```
* 삭제 이상

투플 삭제 시 같이 저장된 다른 정보까지 연쇄적으로 삭제되는 현상이다.
```
```
* 삽입 이상 (해당 하는 값이 없어 NULL 을 입력해야 하는 현상) 

투플 삽입 시 값이 없어서 NULL 값을 입력하면 원치 않는 결과를 조회할 수 있다. NULL 값은 데이터가 없다는 것을 의미한다.

카운트 조회시 NULL 값은 집계되지 않으며, 사칙 연산을 수행할 때 NULL 값과 숫자를 더하게 되면 0이 아닌 NULL + 숫자가 되기 때문에
연산의 결과 NULL 값을 반환하게 된다.

따라서 가능한 테이블에 NULL 값은 없어야 한다.
```
```
* 수정 이상

투플 수정 시 중복된 데이터의 일부만 수정되어 데이터의 불일치 문제가 일어나는 현상이다.
```
### 이상현상의 예시 
```
* 이상 현상 실습을 위한 summer 계절 학기 테이블 

create table summer
(sid integer,
class varchar(20),
price integer);

insert into summer values(100, 'FORTRAN', 20000);
insert into summer values(150, 'PASCAL', 15000);
insert into summer values(200, 'C', 10000);
insert into summer values(250, 'FORTRAN', 20000);
```
```
앞서 설명했듯이 조회를 할 때는 이상현상이 발생하지 않는다. 간단한 SQL 구문을 연습하고 간다. 

select sid,class from summer;
select price 'C 수강료' from summer where class like 'c';
select distinct class from summer where price = (select max(price) from summer); // 부속 질의
select count(*), sum(price) from summer;
```
```
Tip - MySQL 이 제공하는 SAFE UPDATES 모드란?

UPDATE 나 DELETE 중 Error Code: 1175 가 발생할 수 있는데 이 옵션은 UPDATE, DELETE 수행 시 실수를 방지하기 위해
기본키 속성을 사용해서만 가능하도록 한 안전 옵션 때문이다.

SET SQL_SAFE_UPDATES=0; // 옵션 off
SET SQL_SAFE_UPDATES=1; // 옵션 on

간단하게 옵션을 껏다 켤 수 있고 workbench 속성에서 따로 설정할 수 도 있다. 
```

#### 삭제 이상
```
지금 테이블 구조에서 200 번 학생의 게절학기 수강 신청을 취소하면 C 강좌의 수강료를 조회할 수 없게 된다.

delete from summer whre sid=200; // 삭제 후 조회하면 C 강좌도 같이 삭제된 것을 알 수 있다! 
```
#### 삽입 이상 
```
새로운 자바 강좌가 생성되었는데 아직까지 신청한 학생이 없어서 NULL 값을 sid 에 입력하는 경우 집계 함수 사용시 원하지 않는 결과를
조회할 수 있다. (전체 조회시 5 개 투플이 조회되지만, 개별 속성(sid) 조회시 4 개만 조회된다.)

insert into summer values(null, 'java', 25000); // 자바 생성

select count(*) '수강 인원' from summer; // 전체 투플 조회는 5 개로 나오지만
select count(sid) '수강 인원' from summer; // sid 속성 개별 조회시 4 개만 조회된다.
```
