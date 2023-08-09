# SQL 기초 
```
DDL: CREATE, ALTER, DROP 테이블이나 관계 구조를 생성한다
DML: SELECT, INSERT, DELETE, UDPATE 등 검색, 삽입, 수정 삭제 하는 데 사용한다.
DCL: GRANT, REVOKE 데이터의 사용 권한을 관리한다.
```

SQL 쿼리를 연습하고 필요한 SQL 문을 저장한다.

## SELECT 연습
```
select distinct publisher from book; // 중복 제거
```
### where 조건 추가
```
* 비교, 범위

=, <>, <, <=, >, >=
between

select*from book where price between 10000 and 20000;
select*from book where price >= 10000 and price <= 20000;
```
```
* 집합

두 개 이상의 값을 비교할 때 in, not in 연산자를 사용해서 집합의 원소인지 판단할 수 있다.

select*from book where publisher in ('굿스포츠', '대한미디어');
select*from book where publisher not in ('굿스포츠', '대한미디어');
```
```
* 패턴

like 는 문자열 패턴을 비교한다. 찾는 속성이 텍스트 혹은 날짜 데이터를 포함하면 반드시 영문? 작은 따옴표를 사용한다.
(한글 작은 따옴표는 오류가 난다.)

참고로 문자열은 '' 작은 따옴표를 사용하지만 as 로 별칭을 줄 때는 "" 큰 따옴표를 사용한다. 생략 할 수도 있으나 별칭에
공백이 있으면 큰 따옴표를 써야한다.

select*from book where bookname like '_구%';
```
#### + like 검색 시 와일드 카드 사용하기
```
+ : 문자열 연결
% : 0 개 이상의 문자열과 일치
[] : 한 개의 문자와 일치 '[0-5]%' 0-5 사이 숫자로 시작하는 문자
[^] : 한 개의 문자와 불일치 '[^0-5]%'
_ : 특정 위치의 1 개의 문자와 일치
```
#### 복합 조건 
```
논리 연산자 and, or, not 를 사용하면 복합 조건을 명시할 수 있다. 

select * from book where bookname like '%축구%' and price >= 20000;
```
### order by 정렬 (오름차순이 디폴트 값) 
```


```
