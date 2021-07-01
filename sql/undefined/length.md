# length함수 사용하기

length함수는 데이터의 길이를 세어 숫자로 리턴해주는 함수

```sql
select country_name, length(country_name) as 길이
from country;
```

나라 이름의 문자 갯수를 세어서 표현  


