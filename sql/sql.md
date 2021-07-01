# SQL의 기본 개념

### SQL의 명령어에는 크게 3가지 종류가 있다.

1. DDL\(Data Definition Language\)
   * CREATE - 개체 생성
   * DROP - 개체 삭제
   * ALTER - 개체의 속성 및 정의 변경
   * RENAME - 개체의 이름 변경
   * TRUNCATE - 테이블 내의 모든 row\(행\)들을 삭제
2. DML\(Data Manipulation Language\)
   * INSERT - 테이블에 데이터를 신규로 삽입
   * UPDATE - 테이블에 데이터를 새로운 값으로 갱신
   * DELETE - 테이블의 전체 또는 일부 삭제
   * SELECT - 테이블의 데이터를 획득
3. DCL\(Data Control Language\)
   * GRANT - 권한 부여
   * REVOKE - 권한 박탈
   * SET TRANSACTION - 트랜잭션 모드로
   * BEGIN - 트랜잭션의 시작
   * COMMIT - 트랜잭션의 실행
   * ROLLBACK - 트랜잭션 취소
   * SAVEPOINT - 롤백 지점 설정
   * LOCK - 테이블의 자원을 독점

