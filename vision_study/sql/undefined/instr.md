# instr함수 사용하기

특정 문자열의 위치를 숫자로 리턴해주는 함수

instr\(칼럼 값, ‘찾는문자’\)

```sql
select continent as 원본, instr(continent,'A') as instr
from country;
```

A라는 문자가 몇번째에 있는지 str라는 항목에 표시 -&gt; 결과값을 보면 Oceania는 4라고 나오는데, 대소문자를 구분하지 않는다는 것을 알수있음

