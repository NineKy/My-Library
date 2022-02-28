# Basic Select 2

### Revising Aggregations - The Count Function

[https://www.hackerrank.com/challenges/revising-aggregations-the-count-function/problem?isFullScreen=true](https://www.hackerrank.com/challenges/revising-aggregations-the-count-function/problem?isFullScreen=true)

```sql
SELECT COUNT(ID)
FROM (
    SELECT *
    FROM CITY
    WHERE POPULATION>100000
)
----------------------------------------------------------------
SELECT COUNT(ID)
FROM CITY
WHERE POPULATION>100000;
```

난 왜 서브쿼리까지 생각한거지..? 아무튼 어렵지 않은듯





### Revising Aggregations - The Sum Function

[https://www.hackerrank.com/challenges/revising-aggregations-sum/problem?isFullScreen=true\&h\_r=next-challenge\&h\_v=zen](https://www.hackerrank.com/challenges/revising-aggregations-sum/problem?isFullScreen=true\&h\_r=next-challenge\&h\_v=zen)

```sql
SELECT SUM(POPULATION)
FROM CITY
WHERE DISTRICT = 'California';
```

난이도 수준이....?&#x20;





### Revising Aggregations - Averages

{% embed url="https://www.hackerrank.com/challenges/revising-aggregations-the-average-function/problem?h_r=next-challenge&h_r=next-challenge&h_v=zen&h_v=zen&isFullScreen=true" %}

```sql
SELECT AVG(POPULATION)
FROM CITY
WHERE District='California';
```

허허 이지하네요





### Average Population

{% embed url="https://www.hackerrank.com/challenges/average-population/problem?h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_v=zen&h_v=zen&h_v=zen&isFullScreen=true" %}

```sql
SELECT TRUNC(AVG(POPULATION))
FROM CITY
```

소수점으로 나뉘어지는 평균을 단순하게 정수로 버림하는 문제 - TRUNC이라는 함수를 이용하자

TRUNC 함수는 날짜나 숫자를 자르는데 사용하는 함수이다.

이번에 숫자의 경우에는 TRUNC(숫자, 잘라버릴자리수)

그래서 TRUNC(AVG(POPULATION), 3) 이런 경우에는 3번째 자리수까지 삭제하겠다는 의미이다.





### Japan Population

{% embed url="https://www.hackerrank.com/challenges/japan-population/problem?h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_v=zen&h_v=zen&h_v=zen&h_v=zen&isFullScreen=true" %}

그냥 Sum 사용하는 단순한 문제

```sql
SELECT SUM(POPULATION)
FROM CITY
WHERE COUNTRYCODE = 'JPN';
```





### Population Density Difference

{% embed url="https://www.hackerrank.com/challenges/population-density-difference/problem?h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&isFullScreen=true" %}

population이 가장 큰 도시의 값 - 가장 작은 도시의 값

```sql
SELECT MAX(POPULATION) - MIN(POPULATION)
FROM CITY;
```





### The Blunder

{% embed url="https://www.hackerrank.com/challenges/the-blunder/problem?h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&isFullScreen=true" %}

음.. 돈을 작성해서 넣고 있었는데, 0키가 안먹는걸 다 기입하고 나서 깨닳았다. 그래서 원본의 평균 - 0을 뺀 숫자들의 평 균을 계산하고 정수로 반올림해라

```sql
SELECT CEIL(AVG(SALARY) - AVG(REPLACE(SALARY, 0, '')))
FROM EMPLOYEES;
```

오우야....&#x20;

CEIL : 어제했던 TRUNC이 자르는 역할, 즉 버림이였다면 ceil은 정수로 올림해주는 함수인데 여기서 중요한건 소수점 첫째 자리에서 올림하는 함수로 원하는 자리수를 매길 수 없다는 단점이 있다

REPLACE : 특정 문자열을 치환하거나 제거하기 위해서 사용

* REPLACE(컬럼, 찾을문자, 변환할문자)



### Top Earners

{% embed url="https://www.hackerrank.com/challenges/earnings-of-employees/problem?h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&isFullScreen=true" %}

직원&#x20;

월급 \* 월수 하면 현재까지 받은 임금파악하고 가장 높은 임금을 출력하고 그 임금을 받는 사람의 수를 출력

```sql
SELECT *
FROM(
    SELECT MONTHS*SALARY, COUNT(MONTHS*SALARY) 
    FROM EMPLOYEE
    GROUP BY MONTHS*SALARY
    ORDER BY MONTHS*SALARY DESC
)
WHERE ROWNUM=1;
```

아침에 출근해서 꼭 복습하기!





### Weather Observation Station 2

{% embed url="https://www.hackerrank.com/challenges/weather-observation-station-2/problem?h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&isFullScreen=true" %}

LAT\__N을 다 더하고, LONG\_W을 다 더하고 소수점 2째점 까지 나오도록 출력하라_

```sql
SELECT ROUND(SUM(LAT_N), 2), ROUND(SUM(LONG_W), 2)
FROM STATION
```

ROUND 함수는 정수의 반올림을 해주는 함수이고,&#x20;

ROUND(원하는 값, 몇자리까지 남을건지)

요런 식으로 사용할 수 있다





### Weather Observation Station 13

{% embed url="https://www.hackerrank.com/challenges/weather-observation-station-13/problem?h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&isFullScreen=false" %}

38.7880\<LAT\_N<137.2345 사이에 있는 값들의 합 구하고 4자리 수로 잘라라

```sql
SELECT TRUNC(SUM(LAT_N), 4)
FROM STATION
WHERE LAT_N > 38.7880 AND LAT_N < 137.2345;
```





### Weather Observation Station 14

{% embed url="https://www.hackerrank.com/challenges/weather-observation-station-14/problem?h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&isFullScreen=false" %}

LAT\_N 항목에서 137.2345보다 작은 것들 중에서 가장 큰 수를 4자리수까지 잘라

```sql
SELECT *
FROM (
    SELECT TRUNC(LAT_N, 4)
    FROM STATION
    WHERE LAT_N < 137.2345
    ORDER BY LAT_N DESC
)
WHERE ROWNUM=1;
```





### Weather Observation Station 15

{% embed url="https://www.hackerrank.com/challenges/weather-observation-station-15/problem?h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&isFullScreen=false" %}

LAT_N이 137.2345보다 작은 것들 중에 가장 큰 수의 LONG\_W를 4자리로 반올림해서 출력하라_

```sql
SELECT *
FROM (
    SELECT ROUND(LONG_W, 4)
    FROM STATION
    WHERE LAT_N < 137.2345
    ORDER BY LAT_N DESC
)
WHERE ROWNUM=1;
```





### _Weather Observation Station 16_

{% embed url="https://www.hackerrank.com/challenges/weather-observation-station-16/problem?h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&isFullScreen=false" %}

Query the smallest _Northern Latitude_ (_LAT\_N_) from **STATION** that is greater than . Round your answer to  decimal places.

```sql
SELECT ROUND(MIN(LAT_N), 4)
FROM STATION
WHERE LAT_N>38.7780;
```





### Weather Observation Station 17

Query the Western Longitude (LONG_W) where the smallest Northern Latitude(LAT\_N) in STATION is greater than 38.7780. Round your answer to 4 decimal places._

```sql
SELECT *
FROM(
    SELECT ROUND(LONG_W, 4)
    FROM STATION
    WHERE LAT_N > 38.7780
    ORDER BY LAT_N
)
WHERE ROWNUM=1;
```





### Weather Observation Station 18

{% embed url="https://www.hackerrank.com/challenges/weather-observation-station-18/problem?h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&isFullScreen=false" %}

맨해탄 거리 - |a-c| + |b-d|로 구하고 소수점 4자리까지만 출력하라

```sql
SELECT ROUND(
    ABS(MIN(LAT_N)-MAX(LAT_N)) + 
    ABS(MIN(LONG_W)-MAX(LONG_W))
, 4)
FROM STATION;
```

절대값을 표현해주는 함수는 ABS를 사용해서 절대값을 표현한다.





### Weather Observation 19

{% embed url="https://www.hackerrank.com/challenges/weather-observation-station-19/problem?isFullScreen=true" %}

Euclidean Distance = sqrt( (a-b)^2 +(c-d)^2 )로 구하고 소수점 4자 까지만 출력

```sql
SELECT ROUND(
    SQRT(
        POWER(MIN(LAT_N) - MAX(LAT_N), 2) +
        POWER(MIN(LONG_W) - MAX(LONG_W), 2)
    )
, 4)
FROM STATION;
```

SQRT(필드)를 통해서 루트를 매겨주는 함수

POWER(필드, 숫자)를 통해서 필드를 숫자만큼 지수 처리해주는 함수





### Weather Observation 20

{% embed url="https://www.hackerrank.com/challenges/weather-observation-station-20/problem?h_r=next-challenge&h_v=zen&isFullScreen=true" %}

LAT\_N의 중앙값을 소수점 4자리 까지만 출력

```sql
SELECT ROUND(
    MEDIAN(LAT_N), 4
)
FROM STATION;
```

오라클에서는 중앙값을 처리해주는 MEDIAN 함수가 존재





















