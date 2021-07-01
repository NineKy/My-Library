---
description: '+, -,*, /'
---

# 산술 연산자 사용해보기

```sql
create table learnsql.exam_result
(
  name varchar(50),
  math int(10),
  english int(10),
  korean int(10)
)character set utf8;
insert into exam_result values('호날두',98, 65, 56);
insert into exam_result values('메시', 87, 76, 78);
insert into exam_result values('치차리토', 76, 87, 75);
insert into exam_result values('살라', 78, 88, 55);
insert into exam_result values('라모스', 56, 90, 89);
insert into exam_result values('모드리치', 90, 95, 78);
insert into exam_result values('케인', 99, 82, 83);
```

테이블생성, 레코드 삽입

학생별 세 과목 합계

```sql
select name, math, english, korean, math+english+korean as total
from exam_result;
```

원하는 항목들을 더하기 해주고 total이라는 별칭으로 실행한것임

as total은 alias\(별칭\)임

학생별 세 과목 평균

```sql
select name, math, english, korean, (math+english+korean)/3 as avg
from exam_result;
```

원하는 항목을 더하기 해주고, 더한 것들의 숫자인 3을 나누고 avg라는 별칭으로 실행  


