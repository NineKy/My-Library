---
description: '데이터 형을 정의하고 삽입해서 관리하는데 정의를 해둬야 데이터의 정합성을 지키는데 유리해지고, 관리적인 측면에서도 수월해짐'
---

# 형 변환 함수

2-20 MySQL의 데이터 타입

여러가지 있지만 자주 쓰는 것만 기록

CHAR : 고정 길이를 갖는 문자열

VARCHAR : 가변길이를 갖는 문자열

TEXT : 긴 문자열 저장가능

BOOLEAN : 0은 false, 1은 true

INT : 정수값

DOUBLE : 실수값

FLOAT : 부동 소숫점 숫자

DATE : YYYY-MM-DD 형태의 날짜

TIME : HH:MM:SS 형태의 시간

DATETIME : 위의 두개를 합쳐서 출력

2-21 묵시적 형 변환

데이터의 형태를 사용자의 의도에 맞춰서 DB가 알아서 형 변환하여 결과를 출력

```sql
select '100' + '200' from dual;
```

이렇게 입력해도 결과값은 300이 됨

```sql
select concat(82,'는 대한민국 국가 식별 전화번호') from dual;
```

이렇게 입력하면 82를 문자열로 간주해서 의도에 맞게 출력이 됨

2-22 CAST, CONVERT 함수 

CAST : mysql에서 데이터 타입을 서로 변환시켜주는 형 변환 함수

```sql
select cast(100 as char) as num_to_char, cast('100' as int) as char_to_num from dual;
```

cast\(바꿀 항목 as 바꿀 테이터 타입\) 방식으로 변화가능

