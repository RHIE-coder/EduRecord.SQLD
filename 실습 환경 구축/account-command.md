# *사용자 관리 관련 명령어 익히기*
 - 지금 접속된 사용자 이름은?
```sql
SQL> show user;
USER은 "SYSTEM"입니다
```
**`SQL>`** 를 바꾸고 싶다면
```sql
SQL> set sqlprompt "SYSTEM>"
SYSTEM>
```
 - 사용자 계정 생성
```sql
CREATE USER [username] IDENTIFIED BY [password];
```
EXAMPLE
```sql
SYSTEM>CREATE USER rhie IDENTIFIED BY 123456;

사용자가 생성되었습니다.
```
 - 사용자 계정 비밀번호 변경
```sql
ALTER USER [username] IDENTIFIED BY [new password];
```
EXAMPLE
```sql
SYSTEM>ALTER USER rhie IDENTIFIED BY 12341234;

사용자가 변경되었습니다.
```

 - 계정 권한 할당
   - 권한을 할당하는 계정이 관리자 계정이어야 합니다.(sys, system)
   - 접속시 계정 이름을 `system`이라고 하던가 아니면 `sys as sysdba`라고 치면 된다.

```sql
GRANT CREATE 권한명 TO 계정;
----------------------------------
시스템 권한 종류
create user : 데이터베이스 유저 생성 권한
select any table : 모든 유저의 테이블 조회 권한
create any table : 모든 유저의 테이블 생성 권한
create session : 데이터베이스 접속 권한
create table : 테이블 생성 권한
create view : 뷰 생성 권한
create proced user : 프로시저 생성 권한
create sequence : 시퀀스 생성 권한
sysdba : 데이터베이스를 관리하는 최고 권한
sysoper : 데이버베이스를 관리하는 권한
```

 - 계정에 부여된 시스템 권한 확인
```sql
SELECT * FROM DBA_SYS_PRIVX
WHERE GRANTEE = '사용자명';
```
 - 계정에 부여된 롤 확인
```sql
SELECT * FROM DBA_ROLE_PRIVS
WHERE GRANTEE = '사용자명' ;
```
 - 계정에 부여된 롤에 부여된 시스템 권한 확인
```sql
SELECT * FROM DBA_SYS_PRIVS
WHERE GRANTEE = '롤명' ;
```
 - 타 계정에 부여한 객체(테이블 등) 권한 확인
```sql
SELECT * FROM DBA_TAB_PRIVS
WHERE OWNER = '테이블소유자명' ;


또는


SELECT * FROM DBA_TAB_PRIVS
WHERE GRANTEE = '권한부여자명' ;
```
 - 계정이 소유한 모든 테이블 확인
```sql
SELECT 테이블명 FROM USER_TABLES;
```

 - 계정 권한 제거
```sql
REVOKE 권한명(시스템 명령 또는 SQL 명령) ON 테이블명 FROM 계정;
```

 - 계정 삭제
```sql
DROP USER 계정명 CASCADE;
```
 - 현재까지 진행내용 COMMIT하기
```sql
commit;
```
 - COMMIT하기 전 바로 내용으로 되돌리기
```sql
rollback;
```