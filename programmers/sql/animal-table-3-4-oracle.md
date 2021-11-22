# Animal Table 3,4 - Oracle

### 보호소에서 중성화한 동물

보호소에서 중성화 수술을 거친 동물 정보를 알아보려 합니다. 보호소에 들어올 당시에는 중성화[1](https://programmers.co.kr/learn/courses/30/lessons/59045#fn1)되지 않았지만, 보호소를 나갈 당시에는 중성화된 동물의 아이디와 생물 종, 이름을 조회하는 아이디 순으로 조회하는 SQL 문을 작성해주세요.

```sql
-- 코드를 입력하세요
SELECT ins.ANIMAL_ID, ins.ANIMAL_TYPE, ins.NAME
FROM ANIMAL_INS ins, ANIMAL_OUTS outs
WHERE ins.ANIMAL_ID = outs.ANIMAL_ID
    AND ins.SEX_UPON_INTAKE LIKE 'Intact%'
    AND (outs.SEX_UPON_OUTCOME LIKE 'Spayed%' OR outs.SEX_UPON_OUTCOME LIKE 'Neutered%')
;
```

### 있었는데요 없었습니다

관리자의 실수로 일부 동물의 입양일이 잘못 입력되었습니다. 보호 시작일보다 입양일이 더 빠른 동물의 아이디와 이름을 조회하는 SQL문을 작성해주세요. 이때 결과는 보호 시작일이 빠른 순으로 조회해야합니다.

```sql
-- 코드를 입력하세요
SELECT ins.ANIMAL_ID, ins.NAME
FROM ANIMAL_INS ins, ANIMAL_OUTS outs
WHERE ins.ANIMAL_ID = outs.ANIMAL_ID AND ins.DATETIME > outs.DATETIME
ORDER BY ins.DATETIME
```

### 없어진 기록 찾기

천재지변으로 인해 일부 데이터가 유실되었습니다. 입양을 간 기록은 있는데, 보호소에 들어온 기록이 없는 동물의 ID와 이름을 ID 순으로 조회하는 SQL문을 작성해주세요.

```sql
SELECT outs.ANIMAL_ID, outs.NAME
FROM ANIMAL_INS ins, ANIMAL_OUTS outs
WHERE ins.ANIMAL_ID(+) = outs.ANIMAL_ID
	AND ins.ANIMAL_ID IS NULL
ORDER BY ANIMAL_ID;
```

### 중성화 여부

보호소의 동물이 중성화되었는지 아닌지 파악하려 합니다. 중성화된 동물은 `SEX_UPON_INTAKE` 컬럼에 'Neutered' 또는 'Spayed'라는 단어가 들어있습니다. 동물의 아이디와 이름, 중성화 여부를 아이디 순으로 조회하는 SQL문을 작성해주세요. 이때 중성화가 되어있다면 'O', 아니라면 'X'라고 표시해주세요.

```sql
SELECT ANIMAL_ID, NAME, 
    CASE
        WHEN SEX_UPON_INTAKE LIKE '%Neutered%' THEN 'O'
        WHEN SEX_UPON_INTAKE LIKE '%Spayed%' THEN 'O'
    ELSE
        'X'
    END
    AS 중성화
FROM ANIMAL_INS
ORDER BY ANIMAL_ID;
```

```sql
SELECT ANIMAL_ID, NAME, 
    CASE
        WHEN SEX_UPON_INTAKE LIKE '%Intact%' THEN 'X'
    ELSE
        'O'
    END
    AS 중성화
FROM ANIMAL_INS
ORDER BY ANIMAL_ID;
```

### 오랜 기간 보호한 동물(1)

아직 입양을 못 간 동물 중, 가장 오래 보호소에 있었던 동물 3마리의 이름과 보호 시작일을 조회하는 SQL문을 작성해주세요. 이때 결과는 보호 시작일 순으로 조회해야 합니다.

```sql
SELECT *
FROM (
    SELECT ins.NAME, ins.DATETIME
    FROM ANIMAL_INS ins, ANIMAL_OUTS outs
    WHERE ins.ANIMAL_ID = outs.ANIMAL_ID(+) AND outs.ANIMAL_ID IS NULL
    ORDER BY ins.DATETIME
)
WHERE rownum<=3
```

```sql
SELECT ins.NAME, ins.DATETIME
FROM ANIMAL_INS ins, ANIMAL_OUTS outs
WHERE ins.ANIMAL_ID = outs.ANIMAL_ID(+) 
    AND outs.ANIMAL_ID IS NULL
    AND rownum>=3
ORDER BY ins.DATETIME
```

### 오랜 기간 보호한 동물(2)

입양을 간 동물 중, 보호 기간이 가장 길었던 동물 두 마리의 아이디와 이름을 조회하는 SQL문을 작성해주세요. 이때 결과는 보호 기간이 긴 순으로 조회해야 합니다.

```sql
SELECT ANIMAL_ID, NAME
FROM (
    SELECT ins.ANIMAL_ID, ins.NAME, outs.DATETIME-ins.DATETIME AS DATETIME
    FROM ANIMAL_INS ins, ANIMAL_OUTS outs
    WHERE ins.ANIMAL_ID = outs.ANIMAL_ID
    ORDER BY DATETIME DESC
)
WHERE rownum<=2
```

```sql
SELECT *
FROM (
    SELECT ins.ANIMAL_ID, ins.NAME
    FROM ANIMAL_INS ins, ANIMAL_OUTS outs
    WHERE ins.ANIMAL_ID = outs.ANIMAL_ID
    ORDER BY outs.DATETIME - ins.DATETIME DESC
)
WHERE rownum<=2
```
