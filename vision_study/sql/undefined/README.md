# 단일 행 함수 사용

SQL은 여러 함수를 이용해서 출력 값을 변환할 수 있음

DBMS에서 함수가 있는데 내장함수와 사용자 정의 함수가 있음

 - 내장 함수 : 우리가 사용하는 RDBMS에 이미 내장된 함수

 - 사용자 정의 함수 : create function문을 이용해 자신이 필요한 변환 규칙을 적용한 함수

위의 두가지 함수 말고 다른 분류도 가능

 - 단일행 함수 : 한 행의 값을 받아서 특정 규칙과 정의를 통해 변환시키는 함수

 - 복수행 함수  : 여러 행의 값을 한꺼번에 받아서 하나의 행의 결과 값으로 되돌려주는 함수

또 분류해보면 

 - 문자함수 - 숫자함수 - 날짜함수 - 형 변환 함수 - 일반 함수 등

이번에 하는 건 내장함수/단일행 함수/문자함수

```sql
create table country
(
  country_name varchar(100),
  capital_city varchar(100),
  continent varchar(100)
)character set utf8;
insert into country values('USA', 'Washington', 'America');
insert into country values('England', 'London', 'Europe');
insert into country values('S.Korea', 'Seoul', 'Asia');
insert into country values('Australia', 'Canberra', 'Oceania');
insert into country values('Ghana', 'Accra', 'Africa');
insert into country values('Argentina' ,'Buenos aires', 'America');
```

 테이블생성, 레코드삽입  


