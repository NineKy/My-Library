# trim/ltrim/rtrim 함수 사용하기

trim은 어떤 문자열의 양쪽, 즉 왼쪽, 오른쪽의 공백을 없애는 함수고, ltrim과 rtrim은 각각 왼쪽과 오른쪽의 공백만 없애는 함수



```sql
select capital_city as 원본, trim(capital_city) as trim, ltrim(capital_city) as ltrim, rtrim(capital_city) as rtrim
from country;
```

