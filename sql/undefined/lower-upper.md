# lower/upper 함수 사용하기

lower/upper함수는 각 입려된 문자를 소문자/대문자로 변경시키는 함수임

Lower는 소문자로

upper는 대문자로 각각 변경시켜줌

함수\(컬럼\) 이런 방식으로 표현하는 것이 가능

```sql
select country_name as 원본, lower(country_name) as 소문자, upper(country_name) as 대문자
from country;
```

원본, 소문자로 변경된거, 대문자로 변경된거 확인 가능

