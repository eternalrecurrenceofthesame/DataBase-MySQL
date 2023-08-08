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

```
```
* 권한 취소 - revoke 문

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
* 권한 부여 및 회수 상황 실습



```

