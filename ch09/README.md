# 데이터베이스 관리의 개요 (MySQL)
```
데이터베이스는 설계 및 모델링과 정규화를 통한 보수만으로는 안정적으로 유지할 수 없다.

데이터베이스는 하드웨어(CPU RAM HDD), 소프트웨어(DBMS), 네트워크 등이 복합적으로 연결되어 운영된다(프로덕션 환경에서는 더 복잡함)
이 중 하나라도 문제가 생기면 서비스는 중지되거나 제대로 동작하지 않는다.

안정적인 서비스를 제공하기 위해 네트워크, 하드웨어, DBMS 등 중요 부문에 엔지니어를 두어 정기적으로 관리해야 하며 여러 엔지니어 중
데이터베이스를 관리하는 사람을 DBA 라고 한다

DBA 는 데이터베이스 구축부터 운영까지 데이터베이스 시스템의 이상 유무를 점검하고 유지 보수하여 시스템에 문제가 발생했을 때 신속하게
대처해야 한다.
```

### 데이터베이스 관리 업무
```
- 서비스 관리(트랜잭션 및 성능 관리)
- 점검 및 모니터링
- 장애 대처(전력 손실 및 DBMS 에 접속하는 응용 소프트웨어 문제, DBMS 자체적 문제)
- 백업과 복원(주기적인 데이터 파일 백업)

- 사용자 관리 및 권한 관리(DBMS 에 직접 접근하는 사용자)
- 시스템 데이터베이스 관리
- 사용자 데이터베이스 관리(애플리케이션을 이용하는 사용자의 데이터)

- 데이터베이스 저장 공간 관리
- 인덱스 관리(일정한 기준을 넘어서는 인덱스는 재구축을 수행한다.)
```
### 데이터베이스 관리 기본 명령어 실습
```
show database; // 전체 데이터베이스 확인
use 데이터베이스;

show tables; // 테이블 목록 확인
desc 테이블; // 테이블 상세 구조 확인
```
## 보안과 권한

데이터베이스 로그인 단계에서 DBMS 접근을 제한 할 수 있으며 로그인 한 사용자별로 데이터베이스 및 테이블 사용 권한을 제한할 수 있다.

### 로그인 사용자 관리 및 권한 관리
```
DBMS 에 접근하는 사용자(응용 소프트웨어) 는 사용자별 ID 와 비밀번호를 가지고 데이터베이스에 접근한다. 식별에 성공한
사용자라 하더라도 데이터베이스 내에 존재하는 여러 스키마와 테이블에 접근하기 위해서는 접근 권한이 필요하다.
```
#### 1. 로그인 사용자 관리하기
```
create user '사용자이름' identified by '비밀번호'; // 관리자(root) 계정에서 사용자를 생성할 수 있다.
(참고로 root 사용자는 보안상 항상 localhost 에서 접속하도록 되어 있다.)

사용자 이름이 같아도 접속 호스트 이름이 다르면 다른 계정이라고 생각하면 된다.

create user madang@localhost identified by 'madang'; // 로컬 유저 생성 
create user madang@'%.happy.cat' identified by 'madang'; // 특정 사이트(happy.cat) 에서 접속 가능한 사용자 계정 

create user 'madang@localhost' identified by 'madang'; // 부호를 잘못 사용하면 사용자 이름이 'madang@localhost' 가 된다. 위와 별개의 사용자

select*from mysql.user where user like 'madang'; // 유저 조회

drop user '사용자이름'; // 사용자 계정 삭제
drop user mdguest@localhost;
```
### 2. 권한 관리
```
MySQL 에서 각각의 사용자가 접속하면 세션이 생성된다. create user 로 처음 생성된 계정은 세션 생성 권한을 부여하지 않고
ID 만 생성하였기 때문에 실제 데이터베이스에 접속할 수 없다.

세션 생성 권한은 데이터베이스 접속, 테이블 생성 및 조회 같은 데이터베이스 활용을 위한 전반적인 부분에서 필요하다.
참고로 소유한 개체(사용자) 에 대한 사용 권한을 관리하기 위한 명령을 DCL 이라고 한다. 
```
```
* 권한 허가 - grant 문 

grant 권한 [(컬럼리스트)] [on 객체] to {사용자|롤} [with grant option]  // 커맨드 구조

권한 : 허가할 권한을 지정한다. select 등등
컬럼 리스트 : 사용 권한을 부여할 테이블의 열 이름을 지정한다 꼭 () 안 에 표시한다. 권한을 받은 사용자는 테이블의 열 이름을 사용할 수 있다.
객체 : 사용 권한을 부여할 객체 지정 테이블이나 뷰 등의 이름

to 사용자 : 권한을 부여할 사용자 권한 묶음인 role 에 권한을 추가 할 수도 있다.
with grant option : 허가 받은 권한을 다른 사용자에게 다시 부여할 수 있는 옵션 
```
```
* 권한 취소 - revoke 문

revoke 권한 [컬럼리스트] [on 객체] from {사용자|롤} 

grant 한 권한을 취소 회수하는 명령 권한 범위 내에서 권한 취소 가능
```
#### 권한 관리 실습
```
* 실습 유저 생성(root 계정에서 생성 할 수 있다.)

create user madang@localhost identified by 'madang';

create user mdguest@'localhost' identified by 'mdguest';
select*from mysql.user where user like 'mdguest';

create user mdguest2 identified by 'mdguest2';
select*from mysql.user where user like 'mdguest2';

madang, mdguest, mdguest2 총 3 개의 계정을 생성한다.
```
```
* madang 유저에게 모든 권한 부여

grant all privileges on *.* to madang@localhost with grant option; // 모든 권한을 grant option 과 부여
flush privileges; // flush 로 변경 사항 반영
show grants for madang@localhost; // 권한 확인

일반적으로 데이터베이스에서 테이블을 포함한 새로운 개체를 생성하면 그 개체를 생성한 사용자는 개체에 대한 모든 권한을
갖게 된다.

즉 madang 사용자가 테이블을 생성하면 테이블에 대한 입력,수정,삭제 권한을 가지고 사용 권한을 다른 사용자에게 줄 수
있고 권한을 회수 할 수도 있다.

여기서는 root 계정에서 생성한 데이터에 대한 모든 권한을 madang 유저에게 부여하고 grant option 을 설정해서 다른
유저에게 madang 이 가진 권한을 줄 수 있게끔 만들었다. 
```
```
* mdguest 유저에게 book 테이블 select 권한 부여하기 

grant select on madang.book to mdguest@localhost; // mdguest book 테이블 조회 허가
```
```
* mdguest 유저에게 customer 테이블의 select, update 권한 부여(+ with grant option)

grant select, update on madang.customer to mdguest@localhost with grant option;
```
```
* mdguest2 유저에게 book, customer select 부여

grant select on madang.book to mdguest2;
grant select on madang.guest to mdguest2; 
```
```
* mdguest 에 부여된 book, customer select 권한 취소

revoke select on madang.book from mdguest@localhost;
revoke select on madang.customer from mdguest@localhost;


참고로 앞서 mdguest 유저에게 select 및 update 옵션을 주면서 grant option 을 같이 부여하고 mdguset2 에게 select 권한을
부여했였다

기존 mdguest 의 권한을 루트에서 revoke 하면 mdguest2 에 부여된 권한도 같이 revoke 돼야 하지만 MySQL 에서는 상위 권한이
(grant option) 회수되어도 mdguest2 에 부여된 권한은 회수되지 않는다. 
```
## 데이터 베이스 백업과 복원

#### 데이터 베이스 시스템 운영 시 일어날 수 있는 문제들
```
1. 미디어 오류(하드 디스크 고장 및 삭제)
2. 사용자 오류(잘못된 업데이트 및 삭제)
3. 하드웨어 문제(정전)
```

### 백업의 종류

데이터 베이스 백업의 종류에 대해 알아본다 백업은 전체, 차등, 증분 백업으로 나눌 수 있는데 이 방법들은 데이터베이스 뿐만 아니라 파일 시스템, 서버 등에서도 사용되는 공통 개념이다.

```
* 전체 백업

개체(엔티티), 테이블, 데이터 등 전체의 복사본을 만드는 여러 번 전체 백업 하면 데이터가 중복 저장 된다. 또한 데이터 전체를 복사하기
때문에 많은 시간이 소요된다. 최초 데이터베이스를 생성했을 때나 데이터베이스에 변경이 있을 때 수행하는 것이 좋다.
```
```
* 차등 백업

차등 백업은 전체 백업 수행 후 변경된 데이터만 갱신하는 방법이다. 복사본과 차이가 있는 변경점만 백업한다.

첫 번째 작업 시점에 전체 백업이 수행된다. 두 번째 작업 수행후 백업 파일에 갱신 데이터가 저장되고 세 번째 작업을 수행 하면 세 번째 파일에는
두 번째 파일과 세 번째 백업 파일의 데이터가 같이 저장된다.

즉 복원 시에는 첫 번째 백업 파일과 마지막 백업파일만 사용하게 된다. (전체 백업 수행후 갱신 백업 데이터를 한번에 백업한다.)
```
```
* 증분 백업

트랜잭션 로그 파일을 저장하는 백업이다. 로그는 데이터의 입력, 수정, 삭제에 관련된 질의를 순서대로 기록하고 있다. 최초 전체 백업 수행 후
로그 백업을 수행한다.

로그 백업은 로그만 백업하므로 빠르게 수행할 수 있지만 복구 시 많은 시간이 소요되기 때문에 대량의 데이터 작업이 발생한 경우에는 백업을
가급적 사용하지 않는 것이 좋다.

전체 백업이 이루어지고 두 번째 세 번째 ... 작업(백업) 시점에 트랜잭션 로그 백업이 이루어진다. 각 데이터베이스 작업마다 로그 백업이 수행되고
로그 기록을 비운다. 증분 백업(로그 복원) 을 사용하면 최초에 백업한 파일과 두 번째 세 번째 트랜잭션 로그가 모두 필요하다. 512 p
```

### MySQL 에서 데이터베이스 백업 사용해보기

MySQL 에서 백업은 물리적 백업과 논리적 백업으로 나뉜다. 그리고 각 방법별로 운영 환경에 맞는 전체,차등,증분 방법을 적용할 수 있다.

#### + 물리적 백업
```
물리적 백업은 데이터베이스를 구동하기 위해 필요한 모든 파일을 물리적으로 '복사' 하는 방법이다. 운영 체제상의 복사 명령을 통해 수행하거나
백업 소프트웨어를 활용할 수 있다.

실제 DBMS 의 운영 파일 및 데이터 파일을 복사하여 보관하는 방식이므로 DBMS 의 버전이나 운영 중인 운영 체제의 버전이 달라지면 복구할 수 없을
수도 있다.

물리적 백업은 데이터베이스 운영중에 진행하는 콜드 백업과, 중지하고 진행하는 핫 백업이 있다.
```
```
* 콜드 백업 

데이터베이스를 중지하고 백업한다. 전체 백업만 가능하지만 백업 툴을 이용하면 차등 백업도 가능하다.
```
```
* 핫 백업 (열린 백업, 온라인 백업)

운영 중인 데이터베이스 파일을 복사한다. 사용량이 많은 시간대에는 백업을 수행하지 않는 것이 좋다.
MySQL 핫 백업은 XtraBackup 등의 별도 툴을 사용하여 수행할 수 있다.
```
#### + 논리적 백업
```
논리적 백업은 데이터 베이스를 구성하는 물리적 파일을 직접 복사하지 않고 데이터베이스가 가지고 있는 데이터(내용) 를 별도의 파일로
옮겨 백업하는 방법이다. (MySQL 에서 제공하는 mysqldump 를 통해 수행)

논리적 백업은 데이터를 일종의 스크립터 형태로 백업하기 때문에 DBMS 의 버전 및 운영 체제가 달라도 백업된 자료를 좀 더 유연하게
복구할 수 있다. 백업 도중 데이터가 변경되면 백업 시작 시점과 종료 시점의 데이터 불일치 문제가 발생할 수 있으므로 주의한다.
```
#### 실습(Workbench)

MySQL 이 제공하는 논리적 백업을 실습해본다. 

```
* 백업

1. 백업 파일이 저장될 폴더 준비
2. bench 에서 root 계정으로 쿼리 창을 만든다. (madang 데이터베이스 전체를 백업후 복원해본다.)

use madang;
select*from customer; // 데이터베이스 확인

workbench -> [navigator] -> administration -> [management] -> [data export]

export 할 데이터 선택 (madang),  objects to export 에서 모든 사항 체크, export to self-contained file 선택
백업 파일 저장 (경로 지정), <start export> 클릭

3. 백업파일 경로를 확인해서 백업된 파일을 확인한다. 
```
```
* 복원

1. 복원할 파일 체크 (경로 확인)
2. 복원을 위해 기존 테이블 중 아무 거나 하나 삭제해본다.

use madang;
set sql_safe_updates=0;
delete from orders; // 데이터 삭제 
select*from orders;

workbnech -> [navigator] -> administration -> [managemenet] -> [dataimport/restore]

import from self-contained file 선택, 복원 파일 선택, default target schema 클릭 madang 선택
<start import> 클릭 
```

