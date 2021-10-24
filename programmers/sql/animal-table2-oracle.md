# Animal Table2 - Oracle

[https://programmers.co.kr/learn/challenges?tab=all\_challenges](https://programmers.co.kr/learn/challenges?tab=all\_challenges)

## 고양이와 개는 몇 마리 있을까

### 문제 : 동물 보호소에 들어온 동물 중 고양이와 개가 각각 몇 마리인지 조회하는 SQL문을 작성해주세요 이때 고양이를 개보다 먼저 조회해주세요.

```sql
SELECT ANIMAL_TYPE, COUNT(ANIMAL_TYPE)
FROM ANIMAL_INS
GROUP BY ANIMAL_TYPE
ORDER BY ANIMAL_TYPE
```

## 루시와 엘라 찾기

### 문제 : 동물 보호소에 들어온 동물 중 이름이 Lucy, Ella, Pickle, Rogan, Sabrina, Mitty인 동물의 아이디와 이름, 성별 및 중성화 여부를 조회하는 SQL문을 작성해주세요. 이때 결과는 아이디 순으로 정렬

\= 여러가지의 값들을 모아서 출력하라

```sql
SELECT ANIMAL_ID, NAME, SEX_UPON_INTAKE
FROM ANIMAL_INS
WHERE NAME IN ('Lucy', 'Ella', 'Pickle', 'Rogan', 'Sabrina', 'Mitty')
ORDER BY ANIMAL_ID
```

컬럼에서 여러가지의 값을 확인하는 방법은 IN 키워드를 사용하는 방법이다!

어떤 값들을 제외하고 확인하는 방법은 NOT IN 키워드를 사용한다!

## 이름에 el이 들어가는 동물 찾기

문제 : 보호소에 돌아가신 할머니가 기르던 개를 찾는 사람이 찾아왔습니다. 이 사람이 말하길 할머니가 기르던 개는 이름에 'el'이 들어간다고 합니다. 동물 보호소에 들어온 동물 이름 중, 이름에 "EL"이 들어가는 개의 아이디와 이름을 조회하는 SQL문을 작성해주세요. 이때 결과는 이름 순으로 조회해주세요. 단, 이름의 대소문자는 구분하지 않습니다.

```sql
SELECT ANIMAL_ID, NAME
FROM ANIMAL_INS
WHERE ANIMAL_TYPE='Dog' AND LOWER(NAME) LIKE '%el%'
ORDER BY NAME
```

LIKE까지는 기억났다 %가 붙으면 앞뒤로 뭐가있던 상관없이 찾는거였다.

EL이 들어가는 건데 대소문자 상관없어서 소문자, 대문자 like문을 모두 사용해보았지만 틀렸다고 해서 찾아보니까 LOWER(필드) 함수를 사용해서 NAME값들을 모두 소문자로 만들고 el을 찾는 방식을 사용했더라

## 중성화 여부 파악하기

문제 : 보호소의 동물이 중성화되었는지 아닌지 파악하려 합니다. 중성화된 동물은 SEX\_UPON\_INTAKE 컬럼에 'Neutered' 또는 'Spayed'라는 단어가 들어있습니다. 동물의 아이디와 이름, 중성화 여부를 아이디 순으로 조회하는 SQL문을 작성해주세요. 이때 중성화가 되어있다면 'O', 아니라면 'X'라고 표시해주세요.

```sql
SELECT ANIMAL_ID, NAME,
    CASE 
        WHEN SEX_UPON_INTAKE LIKE 'Neutered%' THEN 'O'
        WHEN SEX_UPON_INTAKE LIKE 'Spayed%' THEN 'O'
    ELSE 'X'
    END 
    AS 중성화
FROM ANIMAL_INS
ORDER BY ANIMAL_ID
```

조건문을 활용하는 방법이였다. CASE문 밑에 WHEN+THEN을 사용해서 조건에 맞는 결과를 보여줄 수 있다. 그리고 ELSE을 사용해서 나머지 처리도 가능하다.

## 최솟값 구하기

문제 : 동물 보호소에 가장 먼저 들어온 동물은 언제 들어왔는지 조회하는 SQL 문을 작성해주세요.

```sql
SELECT MIN(DATETIME)
FROM ANIMAL_INS
```

min함수를 사용해서 해당 컬럼들 중에서 가장 최소값을 찾을 수 있다.

## 동물 수 구하기

문제 : 동물 보호소에 동물이 몇 마리 들어왔는지 조회하는 SQL 문을 작성해주세요.

```sql
SELECT COUNT(*)
// SELECT COUNT(ANIMAL_ID)
FROM ANIMAL_INS
```

count함수를 사용해서 원하는 컬럼의 숫자가 몇개가 있는지 확인하는 것이 가능하다.

## 중복 제거하기

문제 : 동물 보호소에 들어온 동물의 이름은 몇 개인지 조회하는 SQL 문을 작성해주세요. 이때 이름이 NULL인 경우는 집계하지 않으며 중복되는 이름은 하나로 친다.

```sql
SELECT COUNT(DISTINCT NAME)
FROM ANIMAL_INS
WHERE NAME IS NOT NULL
```

COUNT 함수를 사용하고, distinct 키워드를 사용해서 중복값을 제거해주었다.

그리고 null 값을 제거해주기 위해서 where 절에서 처리를 해주었다.

## NULL 처리하기

문제 : 입양 게시판에 동물 정보를 게시하려 합니다. 동물의 생물 종, 이름, 성별 및 중성화 여부를 아이디 순으로 조회하는 SQL문을 작성해주세요. 이때 프로그래밍을 모르는 사람들은 NULL이라는 기호를 모르기 떄문에, 이름 없는 동물의 이름은 "No name"으로 표시해주세요

```sql
SELECT ANIMAL_TYPE, NVL(NAME, 'No name') AS NAME, SEX_UPON_INTAKE
FROM ANIMAL_INS
ORDER BY ANIMAL_ID
```

NVL 이라는 함수를 사용했음 : 첫 번째 인자가 null인 경우에 두 번째 인자 값을 반환하는 함수이다.

NVL2(param1, param2, param3) : param1이 null일 경우에는 param2를 반환하고, null이 아닌 경우에는 param3을 반환하는 방법

## 동명 동물 수 찾기

문제 : 동물 보호소에 들어온 동물 이름 중 두 번 이상 쓰인 이름과 해당 이름이 쓰인 횟수를 조회하는 SQL문을 작성해주세요. 이떄 결과는 이름이 없는 동물은 집계에서 제외하며, 결과는 이름 순으로 조회해주세요.

```sql
SELECT NAME, COUNT(NAME) AS COUNT
FROM ANIMAL_INS
GROUP BY NAME HAVING COUNT(NAME)>=2
ORDER BY NAME
```

## 입양 시각 구하기(1)

문제 : 보호소에서는 몇 시에 입양이 가장 활발하게 일어나는지 알아보려 합니다. 09:00부터 19:59까지, 각 시간별대로 입양이 몇 건이나 발생했는지 조회하는 SQL문을 작성해주세요. 이때 결과는 시간대 순으로 정렬해야 합니다.

```sql
SELECT TO_CHAR(DATETIME, 'HH24') AS HOUR, COUNT(*) AS COUNT
FROM ANIMAL_OUTS
GROUP BY TO_CHAR(DATETIME, 'HH24')
HAVING TO_CHAR(DATETIME, 'HH24') BETWEEN 9 AND 19
ORDER BY TO_CHAR(DATETIME, 'HH24')
```

시간을 조회하기 위해서는 to\_char함수를 사용해서 데이터를 추출할 수 있고 추출시 날짜의 포맷을 넣어서 추출할 수 있는데, 이번 문제같은 경우에는 시간을 단위로 잡았기 때문에 'HH24'로 잡아서 조건대로 넣고 정렬해서 출력

## DATETIME에서 DATE로 형 변환

문제 : ANIMAL\_INS 테이블에 등록된 모든 레코드에 대해, 각 동물의 아이디와 이름, 들어온 날짜를 조회하는 SQL문을 작성해주세요. 이때 결과는 아이디 순으로 조회해야 합니다.

```sql
SELECT ANIMAL_ID, NAME, TO_CHAR(DATETIME, 'YYYY-MM-DD') AS 날짜
FROM ANIMAL_INS
ORDER BY ANIMAL_ID
```

이거먼저 풀고 위의 문제를 푸는거구나... 이전의 to\_char 함수를 사용하면 어렵지 않게 풀 수 있는 문제였음
