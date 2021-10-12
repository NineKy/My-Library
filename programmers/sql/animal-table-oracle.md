---
description: Oracle version
---

# Animal Table - Oracle

## 모든 레코드 조회하기

동물 보호소에 들어온 모든 동물의 정보를 ANIMAL\_ID순으로 조회하는 SQL문을 작성해주세요. SQL을 실행하면 다음과 같이 출력되어야 합니다.

```sql
-- 코드를 입력하세요
SELECT * FROM ANIMAL_INS ORDER BY ANIMAL_ID 
```



## 최대값 구하기

가장 최근에 들어온 동물은 언제 들어왔는지 조회하는 SQL 문을 작성해주세요.

```sql
-- 코드를 입력하세요
SELECT MAX(DATETIME) AS 시간
FROM ANIMAL_INS;
```



## 이름이 없는 동물의 아이디

동물 보호소에 들어온 동물 중, 이름이 없는 채로 들어온 동물의 ID를 조회하는 SQL 문을 작성해주세요. 단, ID는 오름차순 정렬되어야 합니다.

```sql
-- 코드를 입력하세요
SELECT ANIMAL_ID
FROM ANIMAL_INS
WHERE NAME is NULL
ORDER BY ANIMAL_ID asc;
```



## 역순 정렬하기

동물 보호소에 들어온 모든 동물의 이름과 보호 시작일을 조회하는 SQL문을 작성해주세요. 이때 결과는 ANIMAL\_ID 역순으로 보여주세요. SQL을 실행하면 다음과 같이 출력되어야 합니다.

```sql
-- 코드를 입력하세요
SELECT NAME, DATETIME
FROM ANIMAL_INS
ORDER BY ANIMAL_ID DESC;
```



## 이름이 있는 동물의 아이디

동물 보호소에 들어온 동물 중, 이름이 있는 동물의 ID를 조회하는 SQL 문을 작성해주세요. 단, ID는 오름차순 정렬되어야 합니다.

```sql
-- 코드를 입력하세요
SELECT ANIMAL_ID
FROM ANIMAL_INS
WHERE NAME is not NULL
ORDER BY ANIMAL_ID ASC;
```





## 아픈 동물 찾기

동물 보호소에 들어온 동물 중 아픈 동물[1](https://programmers.co.kr/learn/courses/30/lessons/59036?language=oracle#fn1)의 아이디와 이름을 조회하는 SQL 문을 작성해주세요. 이때 결과는 아이디 순으로 조회해주세요.

```sql
-- 코드를 입력하세요
SELECT ANIMAL_ID, NAME
FROM ANIMAL_INS
WHERE INTAKE_CONDITION = 'Sick'
ORDER BY ANIMAL_ID;
```





## 어린 동물 찾기

동물 보호소에 들어온 동물 중 젊은 동물[1](https://programmers.co.kr/learn/courses/30/lessons/59037#fn1)의 아이디와 이름을 조회하는 SQL 문을 작성해주세요. 이때 결과는 아이디 순으로 조회해주세요.   


```sql
-- 코드를 입력하세요
SELECT ANIMAL_ID, NAME
FROM ANIMAL_INS
WHERE INTAKE_CONDITION != 'Aged'
ORDER BY ANIMAL_ID ASC;
```



## 동물의 아이디와 이름

동물 보호소에 들어온 모든 동물의 아이디와 이름을 ANIMAL\_ID순으로 조회하는 SQL문을 작성해주세요. SQL을 실행하면 다음과 같이 출력되어야 합니다.

```sql
-- 코드를 입력하세요
SELECT ANIMAL_ID, NAME
FROM ANIMAL_INS
ORDER BY ANIMAL_ID ASC;
```



## 여러 기준으로 정렬하기

동물 보호소에 들어온 모든 동물의 아이디와 이름, 보호 시작일을 이름 순으로 조회하는 SQL문을 작성해주세요. 단, 이름이 같은 동물 중에서는 보호를 나중에 시작한 동물을 먼저 보여줘야 합니다.

```sql
-- 코드를 입력하세요
SELECT ANIMAL_ID, NAME, DATETIME
FROM ANIMAL_INS
ORDER BY NAME ASC, DATETIME DESC;
```





## 상위 n개 레코드

동물 보호소에 가장 먼저 들어온 동물의 이름을 조회하는 SQL 문을 작성해주세요.

```sql
-- 코드를 입력하세요
SELECT *
FROM (select name
    from animal_ins
      order by datetime
     )
     where rownum=1;
```

오라클에서는 limit와 같이 몇개를 가져오는 방식이 따로 없다 때문에 서브 쿼리를 사용해서 모든 값을 가져오고

rownum을 사용한다

=1이면 해당 숫자번째에 있는 값을 가져오는거고

&gt;=이나 여러 부등호를 사용할 수 있는 것 같다





