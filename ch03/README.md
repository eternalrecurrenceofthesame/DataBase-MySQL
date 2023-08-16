# 1. SQL 기초 
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
select*from book order by price desc, publisher asc; // 가격이 같으면 출판사를 오름차순으로 
```
## 집계 함수와 group by 

group by 로 데이터를 집계한 후 구체적인 집계 내용은 집계 함수를 사용한다. (집계함수를 select 에서 사용하면서 값을 조회하려면 group by 를 써야 한다.)

#### 집계 함수의 종류
```
sum([all | distinct] 속성 이름)
avg([all | distinct] 속성 이름)
count({[all|distinct] 속성 이름|*})
max([all | distinct] 속성 이름)
min([all | distinct] 속성 이름)
```
```
고객(custid) 별 주문한 도서의 총 수량 및 총 판매액 구하기

select custid, count(*) as 도서수량 , sum(saleprice) as 총액 from orders group by custid order by custid;
```
### having 절은 group by 내에서 그룹을 제한하는 역할을 한다.

#### group by 사용시 주의점
```
group by 로 투플을 그룹으로 묶은 후 select 절에는 group by 에서 사용한 속성 + 집계함수만 나올 수 있다.

where 절과 having 절을 같이 사용할 때는 규칙이 있다. having 은 반드시 group by 와 함께 사용하며 where 절보다
뒤에 나와야 한다. 그리고 having 의 검색 조건에는 sum,max,avg 같은 집계 함수가 나와야 한다.

참고로 from, where, group by, having select, order by 순으로 sql 문이 실행된다. 165 p 
```
```
8000 원 이상 도서를 두 권 이상 구매한 고객에 대한 고객별 주문 도서의 총 수량을 구한다

select custid, count(*) as 도서수량 from orders where saleprice >= 8000 group by custid having count(*) >=2;
```
## 두 개 이상의 테이블에서 sql 질의 하기

단순히 두 개의 테이블을 합치면 카티전 프로덕트 연산이 수행되기 떄문에 (조건 없는 테이블 간 조인) 조인할 때는 테이블 간 공통 속성을 사용해야 한다. (pk, fk) 
```
테이블 세 개 조인하기 각각의 공통 속성으로 조인한다 20000 만원인 도서를 구매한 고객 이름과 책 제목

select c.name, b.bookname from customer c, orders o, book b where c.custid=o.custid and o.bookid=b.bookid and b.price=20000;
```
#### 셀프 조인이란?
```
셀프 조인은 자신을 대상으로 조인하는 것을 말한다. 셀프 조인을 사용하면 같은 테이블을 중복해서 사용해야 하기 때문에 별칭을 써서 구분한다.


staff(직원) 테이블에서 매니저 blake 가 관리하는 부하 직원의 이름과 직급을 테이블 내에서 조인해서 가져오기

select staff.ename, staff.job from emp staff, emp manager where staff.mgr=manager.empno and manager.ename like 'blake';

각 직원이 가진 매니저의 사원 번호(공통 속성) 를 조인한 다음  복합 조건으로 like 패턴을 추가한다. 
```
### 외부 조인해보기
```
select <속성들> from 테이블 1, 테이블2 ... where <조인 조건> and <검색 조건> // 동등 조인 1 (주로 사용)
select <속성들> from 테이블 1 inner join 테이블2 on <조인 조건> where <검색 조건> // 동등 조인 2

select <속성들> from 테이블 1{left|right|full[outer]} join 테이블2 on <조인 조건> where <검색 조건> // 외부 조인


주문한 고객과 주문하지 않은 고객의 판매 금액 가져오기. 외부 조인으로 주문하지 않은 고객의 정보를 가져온다. 

select c.name, o.saleprice from customer c left outer join orders o on c.custid=o.custid;
```
### 부속 질의 해보기 (sql 문 내에 또 다른 sql 문 작성) 
```
부속 질의란 select 문의 where 절에 또 다른 테이블 결과를 이용하는 것으로써 조인과 다르게 테이블을 합치지 않고 연관된 컬럼의
메타 데이터를 통해서 필요한 값을 조회한다. 

부속 질의로 반환되는 테이블은 단일행열(1*1), 다중행 단일열(n*1), 단일행 다중열(1*n), 다중행열(n*n)이 있다.
부속 질의의 결과 여러 개의 값을 반환하는 다중행 단일열은 in 키워드를 사용한다. ? 173 p 

select bookname from book where price=(select max(price) from book); // 기본 부속 질의 예시 (1*1) 단일행열 최댓값
```
```
* 상위 부속 질의

상위 부속 질의란 하위 부속질의를 먼저 실행하고 그 결과를 이용해 상위 부속 질의를 실행한다.

대한미디어에서 출판한 책을 구매한 고객의 이름을 찾는 3 개 이상의 중첩질의로써 상위 부속 질의가 된다.

select name from customer where custid in (select custid from orders where bookid in
(select bookid from book where publisher='대한미디어'));
```
```
* 상관 부속 질의

상관 부속 질의는 상위 부속 질의의 투플을 이용하여 하위 부속 질의를 계산한다. 상위 부속 질의와 하위 부속 질의가
독립적이지 않고 서로 관련을 맺고 있다.

출판사 별로 출판사의 평균 도서 가격보다 비싼 도서를 구하기 book 테이블 내부에서 서로 관련을 맺는다(내부 조인과 비슷)

select b1.bookname from book b1 where b1.price > (select avg(b2.price) from book b2 where b2.publisher=b1.publisher);
```
### 집합 연산
```
집합 연산은 테이블 간의 집합을 구하는 연산 합집합(union), 차집합(not in), 교집합(in) 을 구할 수 있다.
참고로 MySQL 은 minus, intersect 집합 연산이 없다.

교집합 in 은 조인을 사용해서 풀 수도 있지만 요구 사항이 교집합을 구하는 것이라면 in 부속질의를 이용한 교집합 방식을 사용한다.
```
```
* 합집합 구하기 (union [중복 제거], union all[중복 포함])

대한민국에 거주하는 고객의 이름과 도서를 주문한 고객의 이름

select name from customer where address like '대한민국%'
union
select name from customer where custid in (select custid from orders); // 교집합 사용
```
#### + MySQL 에서 차집합(minus), 교집합(intersect) 사용하기
```
* not in 으로 차집합 구하기

대한민국에서 거주하는 고객의 이름에서 도서를 주문한 고객의 이름만 빼고 조회하기

select name from customer where address like '대한민국%' and name not in
(select name from customer where custid in(select custid from orders)); // 상관 부속 질의는 하위 부속 질의부터 계산한다.
```
```
* in 으로 교집합 구하기

대한민국에 거주하는 고객 중 도서를 주문한 고객의 이름을 보이시오 

select name from customer where address like '대한민국%' and name in
(select name from customer where custid in (select custid from orders));
```

### exists 존재 여부 알아보기

exists 는 상관 부속 질의문 형식으로 사용한다. (상위 부속질의를 사용해서 하위 부속질의를 구한다.) 

```
주문이 있는(exists) 고객의 이름과 주소를 보이시오

select name, address from customer cs where exists(select*from orders od where cs.custid=od.custid);
// 먼저 고객을 조회하고 주문이 있는 고객이 존재하는지 참 거짓을 판단한 후 참인 값을 출력한다.
```
## 데이터 정의어 (DDL - Data Definition Language)

### create 문

#### + 문자형 데이터 타입 - char, varchar
```
char 는 n 바이트를 가진 문자형 타입으로 저장되는 문자의 길이가 n 바이트보다 작으면 나머지는 공백으로 채우며
varchar 는 저장되는 문자의 길이 만큼 가변되는 타입이다,

즉 char 와 varchar 의 데이터가 같다고 해도 char 는 공백을 채운 문자열이기 때문에 동등 비교시 실패할 수 있다. 
```
```
* create 예시

create table newbook(
bookname varchar(20) not null,
publisher varchar(20) unique,
price integer default 10000 check(price>=1000),
primary key(bookname, publisher));

create table newcustomer (
custid integer primary key,
name varchar(40),
address varchar(40),
phone varchar(30));

create table neworders(
orderid integer,
custid integer not null,
bookid integer not null,
saleprice integer,
orderdate date,
primary key(orderid),
foreign key(custid) references newcustomer(custid) on delete cascade);
```
#### + 외래키 제약 조건을 명시할 때 주의할 점
```
참조되는 부모 테이블이 있어야 하며 부모 테이블의 기본키 값을 외래키로 참조해야 한다. 외래키 지정시 ON DELETE 옵션으로
참조되는 부모 테이블의 투플이 삭제될 때의 액션을 지정할 수 있다.

no action - 설정의 기본 값으로 어떠한 동작도 취하지 않는다.
on delete restrict - 자식 릴레이션이 참조하고 있을 경우 부모 릴레이션의 작업 거부 
on delete cascade -자식 릴레이션의 투플도 같이 삭제
on delete set null - NULL 을 허가한 경우 NULL 값으로 변경

참고로 참조되는 부모 테이블 생성시 속성에 default 값을 설정하면 default 값으로 변경된다. 
```
#### + 데이터 타입의 종류

```
integer/int - 4 바이트 정수형을 저장한다.
numeric(m,d)/decimal(m,d) - 전체 자릿수 m, 소수점이하 자릿수 d 를 가진 숫자형 저장
char(n) - 문자형 고정 길이 남은 공간은 공백 
varchar(n) - 문자형 가변 길이
date - 날짜형 연도,월,날,시간을 저장

그외 자료형 MySQL 래퍼런스를 참고 
```
### Alter - 테이블의 속성 및 제약 조건 변경
```
alter table newbook add isbn varchar(13);
alter table newbook modify isbn integer;

alter table newbook drop column isbn;
alter table newbook modify bookid integer not null;

alter table newbook add primary key(bookid)
```
### DROP - 테이블 삭제
```
drop table newbook;

삭제하려는 테이블의 기본키를 다른 테이블에서 참조하고 있을 때 삭제가 거부된다. 참조하는 테이블부터 삭제한다.
```
## 데이터 조작어 - 삽입, 수정 삭제

### insert
```
insert into book(bookid, bookname, publisher, price) values(11, '스포츠 의학', '한솔의학서적', 90000);
// 속성을 입력하지 않으면 속성 순서대로 값을 입력해야 한다. 속성 순서를 바꿔서 입력할 수도 있다.

모든 속성을 전부 입력하지 않아도 된다. 특정 속성만 선택해서 입력할 경우 데이터가 없는 속성은 null 값으로 저장된다.
```
#### + bulk insert
```
select 를 사용해서 같은 타입을 가진 테이블의 데이터 값을 대량으로 insert 할 수도 있다.

imported_book 테이블의 데이터를 book 테이블에 모두 insert 하기

insert into book(bookid, bookname, price, publisher) select bookid, bookname, price, publisher
from imported_book;
```
### update (특정 속성 값을 수정하는 명령)
```
book 의 14 번 출판사를 imported_book 21 번 출판사로 변경하기
update book set publisher=(select publisher from imported_book where bookid ='21') where bookid='14';
```
#### + safe updates 모드와 update 시 주의사항
```
workbench 는 update 나 delete 중 실수 하는 것을 방지하기 위해 기본키 속성을 사용해서만 가능하도록 안전 옵션을
디폴트 값으로 둔다. 

update customer set address='대한민국 부산' where custid=5; // 이런식으로 기본키를 사용한다.

edit - preferences 에서 safe updates 를 체크 해제하면 set sql_safe_updates=0; 커맨드를 사용하지 않고
update, delete 할 수 있다.
```
```
* update 시 주의할 점

safe update 를 해제하고 update customer set address='대한민국 서울'; 이렇게 해버리면 bulk update 가 되어 버린다.
특정 고객의 값을 수정하려고 했다가 모든 값이 벌크로 수정될 수 있으니 update 를 할 때는 항상 주의해야 한다. 
```
### delete 문
```
delete from book where bookid='11';

delete 또한 마찬가지로 특정 투플을 지정하지 않으면 테이블 데이터가 전체 삭제되기 때문에 주의해야 한다.

참고로 drop 을 사용하면 테이블 자체를 삭제해버린다.
```

# 2. SQL 고급 

## 내장 함수 

수학의 함수 y=f(x) 를 SQL 문으로 사용할 수 있다. SQL 에서 함수는 DBMS 가 제공하는 내장 함수와 사용자가 필요에 따라 직접 만드는 사용자 정의 함수 두 개가 있다.

```
실습에서 사용하는 함수 외의 MySQL 에서 제공하는 내장 함수는 아래 래퍼런스를 참고한다.
dev.mysql.com/doc/refman/8.0/en/functions.html
```
### 숫자 함수
```
* 숫자 함수의 종류

abs(숫자) // 절댓값
ceil(숫자) // 숫자보다 크거나 같은 최소의 정수 ceil(4.1) -> 5
floor(숫자) // 숫자보다 작거나 같은 최소 정수 floor(4.1) -> 4
round(숫자,m) // 숫자의 반올림 round(5.36, 1) -> 5.40
power(숫자,n) // 숫자의 n제곱 값을 계산 power(2,3) -> 8
sign(숫자) // 숫자가 음수면 -1, 0 이면 0, 양수면 1
```
#### + from 절이 없는 select 문
```
데이터베이스에 따라 다르지만 mysql 은 from 이 없어도 select 를 사용할 수 있다. 그러나 오라클에서는
from 절이 필수이기 때문에 일시적인 연산을 위해 dual 이라는 가상 테이블을 사용한다.
```
```
* 숫자 함수 연습

select abs(-78), abs(+78); // abs 는 절댓값을 구하는 함수
select round(4.875,1); // round 는 반올림 함수 여기서는 소수 첫째 자리까지 반올림한다.

고객별 평균 주문 금액을 백원 단위로 반올림한 값을 구하기
select custid'고객번호', round(sum(saleprice)/count(*), -2)'평균금액' from orders group by custid;
```
### 문자 함수 

문자 함수는 주로 char, varchar 의 데이터를 대상으로 문자를 가공한 결과를 반환한다.

```
* 문자 함수의 종류

concat(s1,s2) // 두 문자열을 연결 concat('마당', '서점') -> '마당 서점'
lower(s) // 소문자로 변환
lapd(s,n,c) // 왼쪽부터 지정한 자릿수까지 지정한 문자로 채운다. lapd('page 1',10,'*') -> '****page 1'
rpad(s,n,c) // 오른쪽부터 지정한 자릿수까지 지정한 문자로 채운다. rapd('abc', 5, '*') -> 'abc**'

replace(s1,s2,s3) // 대상 문자열의 지정한 문자를 원하는 문자로 변경 replace('jack & jue', 'j', 'bl') -> 'black & blue'

substr(s,n,k) // 대상 문자열의 지정된 자리에서부터 지정된 길이만큼 잘라서 반환 substr('abcdefg',3,4) -> 'cdef'
trim(c from s) // 양쪽에서 지정된 문자를 삭제한다 문자열만 넣으면 공백을 제거한다 trim('=' '==browning==') -> 'browning'

upper(s) // 문자열을 대문자로 변환

length(s) // 대상 문자열의 byte 를 반환 한글은 utf-8 에서 글자당 3 byte 이다
char_length(s) // 문자열의 문자 수를 반환 (바이트가 아닌 문자의 수)
```
```
* 문자 함수 연습

도서 제목에 야구가 포함된 도서를 농구로 변경한 후 도서 목록을 보이시오. (속성을 지정해서 변경)
select bookid, replace(bookname, '야구', '농구') bookname, publisher, price from book;

굿스포츠에서 출판한 도서의 제목과 제목의 문자 수, 바이트 수를 보이시오
select bookname, char_length(bookname) '문자수', length(bookname) '바이트수' from book where publisher='굿스포츠';

마당 서점의 고객 중 같은 성을 가진 사람이 몇 명이나 되는지 성별 인원수를 구하시오
select substr(name, 1,1) '성', count(*)'인원' from customer group by substr(name,1,1);
```
### 날짜 시간 함수

날짜를 단순 문자열이 아닌 날짜형 데이터로 저장하고 관리하면 날짜 연산을 쉽게 처리할 수 있다.
```
* 날짜 시간 함수 종류

str_to_date(string,format) //str_to_date('2019-02-14', '%Y-%m-%d') -> 2019-02-14 문자를 date 자료형으로 반환
date_format(date,format) // date_format('2019-02-14', '%Y-%m-%d') -> '2019-02-14' 날짜형 date 을 문자열varchar 로 반환

adddate(date,interval) // adddate('2019-02-14', interval 10 day) -> 2019-02-24
date(date) // date('2003-12-31 01:02:03'); -> 2003-12-31 문자열의 날짜 부분을 date 로 반환

datediff(date1,date2) // datediff('2019-02-14', '2019-02-04') -> 10 날짜의 차이를 반환 d1-d2
sysdate // sysdate() 시스템상의 오늘 날짜를 반환


참고로 날짜 변수 입력시 - 를 생략하고 연결해서 작성해도 된다. ( - 를 포함하면 - 를 포함한 데이트를 반환함 / 같은 다른 거로 구분해도 된다.)
```
```
* 날짜 시간 함수 연습

마당서점은 주문일로부터 10일 후 매출을 확정한다. 각 주문의 확정일자를 구하시오
select orderid'주문번호', orderdate'주문일', adddate(orderdate, interval 10 day) '확정일' from orders;
```
#### + date_format 을 지정해서 사용하기

앞서 설명했듯 date_format 은 varchar 를 date 타입으로 변경시켜준다. format 의 주요 지정자를 알아보고 간단한 실습을 해본다.

```
* format 주요 지정자

%w // 요일 순서(0~6, sunday=0)
%W // 요일(sunday_saturday)
%a // 요일의 약자(sun~sat)
%d // 1 달 중 날짜 (00~31)
%j // 1 년 중 날짜(001~336)
%h // 12 시간 (01~12)
%H // 24 시간 (00~23)
%i // 분(0~59)
%m // 월 순서 (0~12, january=01)
%b // 월 이름 약어(jan~dec)
%M // 월 이름(january~december)
%s // 초(0~59)
%Y // 4 자리 연도
%y // 4 자리 연도의 마지막 2 자리

모든 포맷터 지정자를 다 사용해보면 이런식으로 나온다.

select sysdate(), date_format(sysdate(), '%w/%W/%a/%d/%j/%h/%H/%i/%m/%b/%M/%s/%Y/%y');
'2023-08-15 05:53:47', '2/Tuesday/Tue/15/227/05/05/53/08/Aug/August/47/2023/23'

참고로 포맷시 지정자, 포맷 형식이 일치하지 않으면 값이 출력되지 않는다. 
```
```
2014 7 7 에 주문받은 도서의 주문번호, 주문일, 고객 번호, 도서 번호를 모두 보이시오 
select orderid , str_to_date(orderdate, '%Y-%m-%d'), custid, bookid from orders where orderdate=date_format('20140707','%Y%m%d');

date_format 으로 varcahr 를 date 로 변경하고 str_to_date 를 적용한다 이렇게 하는 이유는 str_to_date 로 varchar 를 date 로 바꾸고 date 의
기본 포맷인 %Y-%m-%d 에 맞춰서 값을 select 해오기 위함인 거 같다.


dbms 서버에 설정된 날짜 시간 요일 확인 해보기
select sysdate(), date_format(sysdate(), '%Y/%m%d%M%h:%s') 'sysdate_1';
```
### NULL 값 처리하기

null 은 0 이나 빈 문자 공백 등과 다른 특별한 값으로써 아직 지정되지 않은 값을 의미한다. 또한 null 값은 비교 연산이 불가능하며, 숫자 연산을 수행하면 null 값이 반환된다.

```
* null 값에 대한 연산과 집계 함수의 결과

null + 숫자 = null

집계함수를 사용했을 때 null 이 있으면 집계에서 제외된다. sum, avg 함수는 해당하는 행이 하나도 없을 경우 null 을 반환하며
null 값은 계산할 때 포함하지 않는다.

select*from mybook where price is null; // 가격이 null 인 투플 조회
select name '이름', ifnull(phone, '연락처없음') '전화번호' from customer; // ifnull 을 사용하면 null 값을 임의의 값으로 변경할 수 있다
```
### 행 번호 출력하기

@seq:=0; 을 변수로 받아서 행 번호를 출력할 수 있다.

```
순번을 설정하고 앞 순번 두 명만 출력한다. 

set @seq:=0;  
select (@seq:=@seq+1)'순번', custid, name, phone from customer where @seq < 2;
```
## 부속질의 (심화)
```
조인은 테이블을 조인해서 필요한 데이터를 추출한다. 부속 질의는 부속 질의한 테이블에서 조회한 데이터를 바탕으로 주 질의의
데이터를 조회한다.

데이터가 대량일 경우 모두 합쳐서 연산하는 조인 보다 필요한 데이터만 찾아서 공급하는 부속질의의 성능이 더 좋다. 225 p

부속질의에는 스칼라 부속질의(select), 인 라인 뷰(from), 중첩 질의(where) 가 있다.
```
### 스칼라(단일 값) 부속 질의 (select 부속 질의)
```
스칼라 부속 질의는 결과 값을 단일 행, 단일 열의 스칼라 값으로 반환한다. 결과 값이 다중 행이거나 다중 열이라면 어떤
행, 열을 출력해야 하는지 알 수 없어 에러를 출력한다. 결과 값이 없으면 null 을 출력한다.

스칼라 부속 질의는 스칼라 값이 들어갈 수 있는 모든 곳에 사용 가능하며 일반적으로 select, update set 에서 사용한다.
```
```
고객별 판매액을 보이시오(고객 이름과 고객별 판매액 출력)
select (select name from customer cs where cs.custid=od.custid)'name', sum(saleprice)'total' from orders od group by od.custid;


스칼라 부속 질의는 주질의 테이블에서 필요한 데이터를 가져온 후 부속질의의 데이터를 기준으로 최종 결과를 출력한다. 227p

customer 테이블의 custid 는 기본키이므로 customer 테이블에서 유일한 값이다. custid 를 기준으로 결과를 가져오는 부속 질의는
단일 행을 반환한다. 부속 질의의 select 문에는 오직 name 열만 표시되어 있으므로 이 부속 질의는 단일 행, 열만 출력한다.

alter table orders add bname varchar(40); // 실습용 쿼리 추가
update orders set bname='피겨 교본' where bookid=1; // bookid 1 의 null 값 업데이트
```
```
* 스칼라 부속질의를 이용해서 null 값 update 를 일괄적으로 처리하기

update orders set bname=(select bookname from book where book.bookid=orders.bookid);
```
### 인라인 뷰 (from 부속 질의)
```
from 절에는 테이블을 지정하는데 테이블 대신 인라인 뷰 부속질의를 사용해서 일시적으로 만들어진 가상의 뷰 테이블을 사용할 수 있다.
부속질의 결과 반환되는 데이터는 다중 행, 다중 열이어도 상관없다.

다만 주 질의의 특정 컬럼 값을 부속 질의가 상속받아서 사용하는 상관 부속 질의는 사용할 수 없다.
```
```
고객 번호가 2 이하인 고객의 판매액을 보이시오(고객 이름과 고객별 판매 액을 출력한다.)
select cs.name, sum(od.saleprice) from (select custid, name from customer where custid <=2) cs, orders od where cs.custid=od.custid group by cs.name;

cs 뷰 테이블을 만들고 필터링 된 값을 orders 테이블과 조인한다. 조인으로 이 문제를 해결할 수도 있지만 조인 결과 테이블에서
필요없는 데이터를 제거해야 하므로 성능 저하가 발생할 수 있다.
```
### 중첩 질의 (where 부속 질의)
```
중첩 질의는 주 질의의 자료 집합에서 한 행씩 가져와 부속 질의를 수행한다. 연산 결과에 따라 where 절의 조건이 참인지 거짓인지
판단해서 참일 경우 주질의의 해당 행을 출력한다.
```
```
* 비교 연산자

평균 주문 금액 이하의 주문에 대해서 주문 번호와 금액을 보이시오
select orderid, saleprice from orders where saleprice <=(select avg(saleprice) from orders); // 상위 부속 질의

각 고객의 평균 주문 금액보다 큰 금액의 주문 내역에 대해 주문번호, 고객번호, 금액을 보이시오
select orderid, custid, saleprice from orders md where saleprice > (select avg(saleprice) from orders so where md.custid=so.custid); // 상관 부속 질의
```
```
* in, not in


```

