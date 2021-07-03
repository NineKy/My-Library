---
description: '<,>,='
---

# where절에 비교 연산자를 사용해보기

국어 점수가 80점 이상인 사람

```sql
select name
from exam_result
where korean>80;
```

where절에 원하는 조건을 넣으면 가능하다

+BETWEEN a AND b : a와 b 사이에 있는 값을 검색

+ IN\(a,b,c\) : a,b,c 중 어느 하나 인 것을 검색

+ like : 특정 패턴을 가지고 있는 조건을 검색

2-3 order by 절을 사용하여 정렬하여 출력하기

기본적으로 오름차순으로 정렬이 되고 만약 내림차순을 원한다면 desc 옵션을 사용하면됨

```sql
select *
from exam_result
order by math;
```

순서를 나타내고 싶은 항목을 넣어서 활용하면 수학점수가 낮은 것 부터 순서대로 정렬되어 출력됨

```sql
select *
from exam_result
order by math desc;
```

이렇게 order by 뒤에 desc를 활용해주면 수학점수가 높은 것 부터 순서대로 정렬되어 출력됨

```sql
select name, math, english, korean, (math+english+korean)/3 as avg
from exam_result
order by avg desc;
```

이렇게 평균을 내고, 그 평균값이 높은 순으로 출력하는 것이 가능함

```sql
select * from exam_result
order by 3;
```

이렇게 단일 숫자로 표현하는 경우는, 세번째 필드\(korean\)을 대상으로 순서를 따지라는 의미임

=====이렇게 가독성이 떨어지는 것은 별로이지만 알아는 두라

