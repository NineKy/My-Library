---
description: SELECT 문장을 이용하여 원하는 데이터 출력하기
---

# SELECT\_EX

 데이터베이스에서 가로는 row, 세로는 column으로 표현 

 row\(record\)를 가져오는것을 Selection이라고 부르고 column을 가져오는 것을 Projection이라고 함

 desc명령 : describe의 약자로, 테이블의 컬럼이 뭐가 있는지 조회하는 방법

create database learnsql 로 데베 생성 -&gt; 데베를 사용할때는 use learnsql로 선택을 해두고 시작해야함

```sql
create table select_test
(
 name varchar(50),
 dept_cd varchar(1),
 phone varchar(15),
 address varchar(100)
) character set utf8;
```

로 테이블 생성

가장 끝에 있는 character set utf8명령은, 테이블에 한글 데이터가 들어갈 수 있도록 캐릭터 셋을 잡아주는 명령

```sql
desc select_test
```

로 테이블의 컬럼을 확인가능

```sql
INSERT INTO learnsql.select_test VALUES('홍길동', 'A', '01023456789','조 선 한양읍');
INSERT INTO learnsql.select_test VALUES(‘손흥민’, ‘A’, ‘0112345434’, ‘영국 런던’); 
INSERT INTO learnsql.select_test VALUES(‘박찬호’, ‘C’, ‘01023433456’, ‘충남 공주’);
INSERT INTO learnsql.select_test VALUES('김유신', 'D', '0187766645', '신라 경주 ');
INSERT INTO learnsql.select_test VALUES('박나래', 'D', '0192929384', '서울특별시 영등포구 ');
INSERT INTO learnsql.select_test VALUES('강감찬', 'E', '01023432123', '고려');
```

이렇게 데이터 삽입

원하는 데이터만 select하는 방법 : select \* from learnsql.select\_test;

여기에서 \*은 아스타 라고 부르기도 하다. 모든 컬럼을 다 출력하라는 의미

아스타 부분에 production인, 컬럼을 넣으면 컬럼의 데이터만 나오게 됨  


```sql
select name, phone from learnsql.select_test;
```

위의 코드를 실행하면 name과 phone의 데이터만 나오는 것을 확인

where는 조건절임 : 여기에 특정한 조건을 입력하여 조건에 해당하는 데이터만 가지고 오라는 명령임

select \* from select\_test where dept\_cd='A';

select, from, where를 키워드라고 부름

=&gt;

select 출력할 컬럼명

from 테이블명

where 출력할 데이터 조건

Expression이란? 

ㄴ 칼럼의 데이터 외에 다른 문자열이나 내용을 출력하고 싶을때 사용

DISTINCT는 중복된 값을 제외하고 출력하게 한다.

```sql
select distinct dept_cd from select_test;
```

연결 연산자 함수로 칼럼 값을 붙여서 출력-concat\(문자열 또는 컬럼의 값을 연결해주는 함수\)

select concat\(name, '의 부서코드는',dept\_cd,' 입니다.'\) from select\_test;

손흥민이라는 이름을 가진 사람의 row만 concat 문자열 연결 함수를 사용

mysql&gt; select concat\(name, '의 부서코드는', dept\_cd, '입니다.'\)

    -&gt; from select\_test

    -&gt; where name='손흥민';

