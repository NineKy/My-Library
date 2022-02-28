---
description: >-
  https://www.hackerrank.com/challenges/revising-the-select-query/problem?isFullScreen=true
---

# Revising the Select Query

![CITY.jpg](https://s3.amazonaws.com/hr-challenge-images/8137/1449729804-f21d187d0f-CITY.jpg)

Query all columns for all American cities in the **CITY** table with populations larger than `100000`. The **CountryCode** for America is `USA`.&#x20;

```sql
select * from CITY where population>100000 AND countrycode='USA';
```



Query the **NAME** field for all American cities in the **CITY** table with populations larger than `120000`. The _CountryCode_ for America is `USA`.

```sql
SELECT name FROM city WHERE population>120000 AND countrycode='USA';
```



Query all columns (attributes) for every row in the **CITY** table.

```sql
SELECT * from CITY;
```



Query all columns for a city in **CITY** with the _ID_ `1661`.

```sql
SELECT * FROM CITY WHERE ID='1661';
```



Query the names of all the Japanese cities in the **CITY** table. The **COUNTRYCODE** for Japan is `JPN`.&#x20;

```sql
SELECT name FROM CITY where countrycode='JPN';
```





Query a list of **CITY** and **STATE** from the **STATION** table. \
The **STATION** table is described as follows: \
![](https://s3.amazonaws.com/hr-challenge-images/9336/1449345840-5f0a551030-Station.jpg)

where **LAT\_N** is the northern latitude and **LONG\_W** is the western longitude.

```sql
SELECT city, state FROM station;
```



Query a list of **CITY** names from **STATION** for cities that have an even **ID** number. Print the results in any order, but exclude duplicates from the answer.&#x20;

```sql
SELECT DISTINCT city FROM station WHERE MOD(ID, 2)=0;
```



Find the difference between the total number of **CITY** entries in the table and the number of distinct **CITY** entries in the table. \
The **STATION** table is described as follows:

![](https://s3.amazonaws.com/hr-challenge-images/9336/1449345840-5f0a551030-Station.jpg)

where **LAT\_N** is the northern latitude and **LONG\_W** is the western longitude.

```sql
SELECT COUNT(city) - COUNT(DISTINCT city) FROM station;
```





#### Weather Observation Station 5

Query the two cities in **STATION** with the shortest and longest _CITY_ names, as well as their respective lengths (i.e.: number of characters in the name). If there is more than one smallest or largest city, choose the one that comes first when ordered alphabetically.&#x20;



**Sample Input**

For example, **CITY** has four entries: **DEF, ABC, PQRS** and **WXY**.

**Sample Output**

```
ABC 3
PQRS 4
```

요놈은 생각하기 좀 힘들었다.. 내일 다시 곱씹어보자..! -> 1.30:킹만하쥬?

```sql
SELECT CITY, LENGTH(CITY)
FROM (
    SELECT CITY, LENGTH(CITY)
    FROM STATION
    ORDER BY LENGTH(CITY), CITY
)
WHERE ROWNUM=1;

SELECT CITY, LENGTH(CITY)
FROM (
    SELECT CITY, LENGTH(CITY)
    FROM STATION
    ORDER BY LENGTH(CITY) DESC, CITY
)
WHERE ROWNUM=1;
```



#### Weather Observation Station 6

Query the list of _CITY_ names starting with vowels (i.e., `a`, `e`, `i`, `o`, or `u`) from **STATION**. Your result _cannot_ contain duplicates.

DISTINCT로 하나만 출력되게 하고, WHERE 절에서 LIKE을 사용하고 OR조건을 사용해서 찾는 방식

정규표현식을 사용해서도 가능하구나 정도? ->[https://yongku.tistory.com/entry/HackerRank-Weather-Observation-Station-6-오라클Oracle](https://yongku.tistory.com/entry/HackerRank-Weather-Observation-Station-6-%EC%98%A4%EB%9D%BC%ED%81%B4Oracle)에서 알게되었음&#x20;

```sql
SELECT DISTINCT CITY
FROM STATION
WHERE (
    CITY LIKE 'A%' OR
    CITY LIKE 'E%' OR
    CITY LIKE 'I%' OR
    CITY LIKE 'O%' OR
    CITY LIKE 'U%'
);



SELECT DISTINCT CITY
FROM STATION
WHERE (
    REGEXP_LIKE(CITY, '^A|^E|^I|^O|^U')
);
```



#### Weather Observation Station 7

Query the list of _CITY_ names ending with vowels (a, e, i, o, u) from **STATION**. Your result _cannot_ contain duplicates.

위 문제와 같은 맥락이다. 밑에 방식도 매우 흥미로웠다..! sql을 짤라서 비교하는 방식도 있다는 것이..!

```sql
SELECT DISTINCT CITY
FROM STATION
WHERE(
    CITY LIKE '%a' OR
    CITY LIKE '%e' OR
    CITY LIKE '%i' OR
    CITY LIKE '%o' OR
    CITY LIKE '%u' 
);

---------------------------------------------------------------------------

SELECT DISTINCT CITY
FROM STATION
WHERE SUBSTR(CITY, LENGTH(CITY), LENGTH(CITY)) 
    IN ('a','e','i','o','u');
    
---------------------------------------------------------------------------
SELECT DISTINCT CITY
FROM STATION
WHERE CITY REGEXP '[aeiou]$';

```



#### Weather Observation Station 8

Query the list of _CITY_ names from **STATION** which have vowels (i.e., _a_, _e_, _i_, _o_, and _u_) as both their first _and_ last characters. Your result cannot contain duplicates.

음.. 그렇게 어렵지 않게 푸니까 이렇게 나오는데 어떻게 더 간단하게 할 수 있을까를 생각해보자

```sql
SELECT DISTINCT CITY
FROM (
    SELECT CITY
    FROM STATION
    WHERE (
        CITY LIKE 'A%' OR
        CITY LIKE 'E%' OR
        CITY LIKE 'I%' OR
        CITY LIKE 'O%' OR
        CITY LIKE 'U%'
    )
)
WHERE (
    CITY LIKE '%a' OR
    CITY LIKE '%e' OR
    CITY LIKE '%i' OR
    CITY LIKE '%o' OR
    CITY LIKE '%u'
);

---------------------------------------------------------------------------
SELECT DISTINCT CITY
FROM STATION
'^[aeiou]*[aeiou]$'
```



#### Weather Observation Station 9

Query the list of _CITY_ names from **STATION** that _do not start_ with vowels. Your result cannot contain duplicates.

위의 쿼리는 할만한디... 문제는 밑에 쪽 쿼리다 -> 정규 표현식을 사용하는 방법을 조금 더 알아보

```sql
SELECT DISTINCT CITY
FROM STATION
WHERE NOT (
    CITY LIKE 'A%' OR
    CITY LIKE 'E%' OR
    CITY LIKE 'O%' OR
    CITY LIKE 'I%' OR
    CITY LIKE 'U%'
);

---------------------------------------------------------------------------
SELECT DISTINCT CITY
FROM STATION
WHERE NOT REGEXP_LIKE(CITY, '^[AEIOU]');
```



#### Weather Observation Station 10

Query the list of _CITY_ names from **STATION** that _do not end_ with vowels. Your result cannot contain duplicates.

```sql
SELECT DISTINCT CITY
FROM STATION
WHERE NOT REGEXP_LIKE(CITY, '[aeiou]$');
```

시작에 대한 정규식을 사용하는 방법은 ^\[무슨알파벳\~] 이렇게 사용하고

끝나는 것에 대한 정규식을 사용하는 방법은 \[무슨알파벳\~]$ 이렇게 사용한다!





#### Weather Observation Station 11

Query the list of _CITY_ names from **STATION** that either do not start with vowels or do not end with vowels. Your result cannot contain duplicates.

```sql
SELECT DISTINCT CITY
FROM STATION
WHERE 
    NOT REGEXP_LIKE(CITY, '^[AEIOU]') 
    OR
    NOT REGEXP_LIKE(CITY, '[aeiou]$');
    
```





#### Weather Observation Station 12

Query the list of _CITY_ names from **STATION** that _do not start_ with vowels and _do not end_ with vowels. Your result cannot contain duplicates.

```sql
SELECT DISTINCT CITY
FROM STATION
WHERE 
    NOT REGEXP_LIKE(CITY, '^[AEIOU]') 
    AND
    NOT REGEXP_LIKE(CITY, '[aeiou]$');
```







