---
description: '숫자, 문자, 날짜등과 관련없이 쓰일 수 있는 함수'
---

# 일반함수란

2-23 ifnull 함수 사용하기

null인 데이터 값이 있을 때, null이라고 출력하지 말고 지정한 특정한 값으로 출력하게 하는 함수

\*null != 0

ㄴnull은 데이터가 없다는 뜻이지만 0은 0이라는 데이터가 들어있다는 의미임

```sql
select name, dept, salary, ifnull(bouns, 0) from class.salary
```

는 보너스자리에서 null인 값들을 0으로 바꿔서 출력하라는 의미

ifnull\(bouns, ‘해당없음’\)이면 

null인 값들을 해당없음 으로 바꿔서 출력됨

ifnull\(bouns, name\)이면 

null인 값들이 해당 record의 name필드의 값이 출력됨

2-24 if 함수 사용하기 =&gt; ifnull함수를 대체할 수 있음

if\(조건, 조건 성립시, 조건 미성립시\)

```sql
select name, dept, salary, if(salary>=300, ‘고액연봉자’, ‘일반연봉자’) from class.salary
```

salary값이 300보다 크거나 같으면 고액연봉자가 출력, 300보다 작으면 일반연봉자가 출력

2-25 case 함수 사용하기

```sql
select name, 
case when dept_cd='A' then '한국'
 when dept_cd='B' then '중국'
 when dept_cd='C' then '일본'
 when dept_cd='D' then '미국'
 when dept_cd='E' then '영국' end as dept_cd
,phone,address
from select_test;
```

해당 컬럼에서 

case when 컬럼이름=이거일때 then 이걸로 변경 …. end as 컬럼이름

이형태로 해주면 정상적으로 바뀌어서 나옴

2-26 복합해서사용해보기  


