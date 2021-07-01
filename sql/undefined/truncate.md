# truncate 함수 사용하기

round와 사용법과 옵션의 의미는 같다. 하지만 반올림이 아니라 버림을 이용한 함수임

```sql
select truncate(112.3456,1), truncate(112.3456,2), truncate(112.3456,-1)
from dual;
```

위에서 round는 옵션이 2가 붙었을떄 2까지 표현하되, 뒤의것을 반올림해서 112.35가 되었지만 truncate는 버림을 이용하기 때문에 112.34가 출력됨

