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
select*from book order by price desc, publisher asc; // 가격이 같으면 출판사를 오름차순으로 
```
## 집계 함수와 group by 

group by 로 데이터를 집계한 후 구체적인 집계 내용은 집계 함수를 사용한다. (집계함수를 사용하려면 group by 를 써야 한다.)

#### 집계 함수의 종류
```
sum([all | distinct] 속성 이름)
avg([all | distinct] 속성 이름)
count({[all|distinct] 속성 이름|*})
max([all | distinct] 속성 이름)
min([all | distinct] 속성 이름)
```
```
select custid, count(*) as 도서수량 , sum(saleprice) as 총액 from orders group by custid order by custid;
// 고객(custid) 별 주문한 도서의 총 수량 및 총 판매액 구하기
```
### having 절은 group by 내에서 그룹을 제한하는 역할을 한다.

#### group by 사용시 주의점
```
group by 로 투플을 그룹으로 묶으면 select 절에서 group by 에서 사용한 속성과 집계 함수만 나올 수 있다.

where 절과 having 절을 같이 사용할 때는 규칙이 있다. having 은 반드시 group by 와 함께 사용하며 where 절보다
뒤에 나와야 한다. 그리고 having 의 검색 조건에는 sum,max,avg 같은 집계 함수가 나와야 한다.

참고로 from, where, group by, having select, order by 순으로 sql 문이 실행된다. 165 p 
```
```
select custid, count(*) as 도서수량 from orders where saleprice >= 8000 group by custid having count(*) >=2;
// 8000 원 이상 도서를 두 권 이상 구매한 고객에 대한 고객별 주문 도서의 총 수량을 구한다
```
## 두 개 이상의 테이블에서 sql 질의 하기

단순히 두 개의 테이블을 합치면 카티전 프로덕트 연산이 수행되기 떄문에 (조건 없는 테이블 간 조인) 조인할 때는 테이블 간 공통 속성을 사용해야 한다. (pk, fk) 
```
select c.name, b.bookname from customer c, orders o, book b where c.custid=o.custid and o.bookid=b.bookid and b.price=20000;
// 테이블 세 개 조인하기 각각의 공통 속성으로 조인한다 20000 만원인 도서를 구매한 고객 이름과 책 제목
```
#### 셀프 조인이란?
```
셀프 조인은 자신을 대상으로 조인하는 것을 말한다. 셀프 조인을 사용하면 같은 테이블을 중복해서 사용해야 하기 때문에 별칭을 써서 구분한다.

select staff.ename, staff.job from emp staff, emp manager where staff.mgr=manager.empno and manager.ename like 'blake';
// staff(직원) 테이블에서 매니저 blake 가 관리하는 부하 직원의 이름과 직급을 테이블 내에서 조인해서 가져온다.

각 직원이 가진 매니저의 사원 번호(공통 속성) 를 조인한 다음  복합 조건으로 like 패턴을 추가한다. 
```
### 외부 조인해보기
```
select <속성들> from 테이블 1, 테이블2 ... where <조인 조건> and <검색 조건> // 동등 조인 1 (주로 사용)
select <속성들> from 테이블 1 inner join 테이블2 on <조인 조건> where <검색 조건> // 동등 조인 2

select <속성들> from 테이블 1{left|right|full[outer]} join 테이블2 on <조인 조건> where <검색 조건> // 외부 조인

select c.name, o.saleprice from customer c left outer join orders o on c.custid=o.custid;
// 외부 조인으로 주문하지 않은 고객의 정보를 가져온다. 
```
### 부속 질의 해보기 (sql 문 내에 또 다른 sql 문 작성) 
```
부속 질의란 select 문의 where 절에 또 다른 테이블 결과를 이용하는 것으로써 조인과 다르게 테이블을 합치지
않고 연관된 컬럼의 메타 데이터를 통해서 필요한 값을 조회한다. 

부속 질의로 반환되는 테이블은 단일행열(1*1), 다중행 단일열(n*1), 단일행 다중열(1*n), 다중행열(n*n)이 있다.
부속 질의의 결과 여러 개의 값을 반환하는 다중행 단일열은 in 키워드를 사용한다.

select bookname from book where price=(select max(price) from book); // 기본 부속 질의 예시 
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
집합 연산은 테이블 간의 집합을 구하는 연산 합집합(union), 차집합(not in), 교집합(in) 을 구할 수 있다. 참고로 MySQL 은
minus, intersect 집합 연산이 없다.

참고로 교집합 in 은 조인을 사용해서 풀 수도 있지만 요구 사항이 교집합을 구하는 것이라면 in 부속질의를 이용한 교집합 방식을
사용한다.
```
```
* 합집합 구하기 (union [중복 제거], union all[중복 포함])

select name from customer where address like '대한민국%'
union
select name from customer where custid in (select custid from orders); // 교집합 사용

대한민국에 거주하는 고객의 이름과 도서를 주문한 고객의 이름
```







