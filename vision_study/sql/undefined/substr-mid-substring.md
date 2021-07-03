# substr/mid/substring 함수 사용

다 같은 함수임-사용법도 동일, 리턴값도 동일

substr\(컬럼명, 시작할 문자열의 위치 값, 리턴시킬 값의 길이\)

```sql
select continent as 원본, substr(continent,2,2) as substr, mid(continent,2,2) as mid, substring(continent,2,2) as substring
from country;
```

이걸로 두번째 자리를 시작으로 두개의 문자열을 출력하는 것이 같고 세 함수 모두 같은 값을 리턴함

만약 출력해야하는 문자열의 크기가 실제 문자열의 숫자를 넘어가면 그냥 끝까지만 리턴한다.

