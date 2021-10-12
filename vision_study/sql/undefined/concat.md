# concat함수 사용

concat\(컬럼값, 컬럼값, ‘문자열’, ’문자열’\) 방식으로 사용가능

```sql
select concat(country_name, '의 수도는', capital_city, '입니다!') as 소개
from country;
```

원하는 컬럼값과 문자열을 붙혀서 출력하는 것이 가능함

