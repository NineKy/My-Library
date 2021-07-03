# lpad/rpad 함수 사용하기

데이터가 있고, 해당 데이터가 어떤 기준보다 짧을 경우에 원하는 문자를 왼쪽이나 오른쪽으로 자릿수를 맞춰 채워주는 함수

Ipad\(칼럼명, 기준 자릿수, 채워넣을 숫자 or 문자\)

```sql
select continent as 원본, lpad(continent,10,'A') as lpad, rpad(continent, 10, 'A') as rpad 
from country;
```

lpad에는 문자 10개를 기준으로 America는 7개니까 lpad는 그 앞에 A를 3개 붙히고  rpad는 그 뒤에 A를 3개 붙힌 것을 확인할 수 있음

