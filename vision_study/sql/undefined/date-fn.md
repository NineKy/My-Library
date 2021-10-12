---
description: 저장되어 있는 많은 데이터들의 이름표가 될 수 있고 특정 데이터를 찾기 위한 키가 될 수 있음
---

# Date fn\(날짜 함수\)

2-14 현재 날짜, 시간출력하기

```sql
select now() = select sysdate() = select current_timestamp() === 2020-07-20 16:03:43
select curdate() = select current_date() ===  2020-07-20 
select current_time() === 16:04:53
select now()+0 === 그냥 now()에서 숫자만 이은 문자열
select current_time()+0 === 그냥 current_time()에서 숫자만 이은 문자열 

```

2-15 날짜, 시간에 따른 특정 정보 출력하기

```sql
select dayofweek(‘2020-07-20 16:03:43’); -> 숫자가 나오게되는데 1:일요일, 2:월요일 …. 7:토요일
select weekday(‘2020-07-20 16:03:43’); -> 위와 같지만 0:월요일, 1: 화요일 … 6:일요일
select dayofmonth(‘2020-07-20 16:03:43’); -> 20 / 날짜만 뽑아서 출력
select dayofyear(‘2020-07-20 16:03:43’); -> 202 / 365일중에 몇일인지 출력
select month(‘2020-07-20 16:03:43’); -> 7 / 월만 뽑아서 출력
select dayname(‘2020-07-20 16:03:43’); -> Monday / 요일을 영문으로 출력
select monthname(‘2020-07-20 16:03:43’); -> July / 월을 영문으로 출력
select quarter(‘2020-07-20 16:03:43’); -> 3 / 분기를 출력(1분기-4분기)
select week(‘2020-07-20 16:03:43’); -> 29 / 1년중 몇번쨰 주인지 출력
select year(‘2020-07-20 16:03:43’); -> 2020 / 년도를 뽑아서 출력
select hour(‘2020-07-20 16:03:43’); -> 16 / 시간을 출력
select minute(‘2020-07-20 16:03:43’); -> 3 / 분을 출력

```

2-16 날짜, 시간을 연산하여 출력하기

날짜, 시간을 연산한다 = 특정 날짜에서 몇일 뒤, 전을 출력하거나 시간을 더하거나 뺴서 출력한다는 의미

date\_add, date\_sub, adddate, subdate

```sql
select date_add('2020-07-20 16:03:43', interval 1 second);
```

입력한 시간에서 1초를 더해서 출력

```sql
select date_add('2020-07-20 16:03:43', interval '1:1' minute_second);
```

입력한 시간에서 1분과 1초를 더해서 출력

```sql
select date_add('2020-07-20 16:03:43', interval 31 day);
```

입력한 시간에서 31일을 더해서 출력

```sql
select date_add('2020-07-20 16:03:43', interval '-1 17' day_hour);
```

입력한 시간에서 하루를 빼고 17시간을 더해서 출력

2-17 시간과 초 데이터 변환하여 출력하기

select sec\_to\_time\(3000\); =&gt; 3000초를 환산하면 50분이 됨

select time\_to\_sec으로 반대로 계산이 가능

2-18 period\_add, period\_diff를 이용하여 원하는 값 출력

period\_add는 입력된 년월 데이터에 원하는 만큼의 개월을 더한 값을 YYYYMM형태로 출력

```bash
mysql> select period_add(2001,15);
+---------------------+
| period_add(2001,15) |
+---------------------+
|              202104 |
+---------------------+
1 row in set (0.00 sec)
```

```bash
mysql> select period_add(202001, 15);
+------------------------+
| period_add(202001, 15) |
+------------------------+
|                 202104 |
+------------------------+
1 row in set (0.00 sec)
```

처음처럼 2020중에 앞에 20을빼고 뒤에 20만 입력하나 밑에꺼처럼 2020 다 입력하나 결과값은 같음

2-19 date\_format함수 사용하여 출력하기

\*가장 많이 사용하는 날짜함수임 : 간단한 파라미터 조정으로 원하는 데이터를 원하는 형태로 쉽게 변경하여 출력가능하다는 장점이 있음

select date\_formate\(‘날짜’, ‘변수’\) 형태를 이용하는데 format변수는 아주 많음…. 검색해서 활용

```sql
select date_format('2020-07-20 16:03:43', '%p');
```

PM 오전인지 오후인지 판별해서 출력

```sql
select date_format('2020-07-20 16:03:43', '%u');
```

해당 년도에서 몇번쨰 주인지 출력  


