# 집합 연산자 사용하기

```sql
create table learnsql.exam_result_2
(
  name varchar(50),
  math int(10),
  english int(10),
  korean int(10)
)character set utf8;
insert into exam_result_2 values('차범근', 78, 90, 78);
insert into exam_result_2 values('서정원', 68, 99, 68);
insert into exam_result_2 values('고종수', 84, 96, 98);
insert into exam_result_2 values('박지성', 67, 68, 75);
insert into exam_result_2 values('최순호', 88, 93, 68);

```

새로운 테이블, 레코드 생성

집합연산자 종류

UNION : 두 집합을 더해서 결과를 출력한다, 중복 값을 제거하고 정렬을 수행

UNION ALL : 두 집합을 더해서 결과를 출력하는데, 중복 값을 제거하지 않고 정렬되지 않음

INTERSECT : 두 집합의 교집합 결과를 정렬하여 출력한다.

MINUS : 두 집합의 차집합 결과를 정렬하고 출력한다.

```sql
select * from exam_result
union
select * from exam_result_2;
```

이렇게 처음에 만든 T와 두번째로 만든 T의 모든 레코드 값을 확인할 수 있음

```sql
select * from exam_result
union 
select * from exam_result;
select * from exam_result
union all
select * from exam_result;
```

는 똑같은 이름들이 두개씩 있는 것을 확인할 수 있음 = 중복을 제거하지 않고 정렬도 되지 않는다

INTERSECT =&gt; 교집합이다

MINUS =&gt; 차집합이다

그림으로 생각하면 편하다

  


[https://stricky.tistory.com/210](https://stricky.tistory.com/210)

