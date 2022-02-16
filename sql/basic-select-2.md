# Basic Select 2

#### Revising Aggregations - The Count Function

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





#### Revising Aggregations - The Sum Function

[https://www.hackerrank.com/challenges/revising-aggregations-sum/problem?isFullScreen=true\&h\_r=next-challenge\&h\_v=zen](https://www.hackerrank.com/challenges/revising-aggregations-sum/problem?isFullScreen=true\&h\_r=next-challenge\&h\_v=zen)

```sql
SELECT SUM(POPULATION)
FROM CITY
WHERE DISTRICT = 'California';
```

난이도 수준이....?&#x20;





#### Revising Aggregations - Averages

{% embed url="https://www.hackerrank.com/challenges/revising-aggregations-the-average-function/problem?h_r=next-challenge&h_r=next-challenge&h_v=zen&h_v=zen&isFullScreen=true" %}

```sql
SELECT AVG(POPULATION)
FROM CITY
WHERE District='California';
```

허허 이지하네요





#### Average Population

{% embed url="https://www.hackerrank.com/challenges/average-population/problem?h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_v=zen&h_v=zen&h_v=zen&isFullScreen=true" %}

```sql
SELECT TRUNC(AVG(POPULATION))
FROM CITY
```

소수점으로 나뉘어지는 평균을 단순하게 정수로 버림하는 문제 - TRUNC이라는 함수를 이용하자

TRUNC 함수는 날짜나 숫자를 자르는데 사용하는 함수이다.

이번에 숫자의 경우에는 TRUNC(숫자, 잘라버릴자리수)

그래서 TRUNC(AVG(POPULATION), 3) 이런 경우에는 3번째 자리수까지 삭제하겠다는 의미이다.





#### Japan Population

{% embed url="https://www.hackerrank.com/challenges/japan-population/problem?h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_v=zen&h_v=zen&h_v=zen&h_v=zen&isFullScreen=true" %}

그냥 Sum 사용하는 단순한 문제

```sql
SELECT SUM(POPULATION)
FROM CITY
WHERE COUNTRYCODE = 'JPN';
```





#### Population Density Difference

{% embed url="https://www.hackerrank.com/challenges/population-density-difference/problem?h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&isFullScreen=true" %}

population이 가장 큰 도시의 값 - 가장 작은 도시의 값

```sql
SELECT MAX(POPULATION) - MIN(POPULATION)
FROM CITY;
```





#### The Blunder

{% embed url="https://www.hackerrank.com/challenges/the-blunder/problem?h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&isFullScreen=true" %}

음.. 돈을 작성해서 넣고 있었는데, 0키가 안먹는걸 다 기입하고 나서 깨닳았다. 그래서 원본의 평균 - 0을 뺀 숫자들의 평 균을 계산하고 정수로 반올림해라

```sql
SELECT CEIL(AVG(SALARY) - AVG(REPLACE(SALARY, 0, '')))
FROM EMPLOYEES;
```

오우야....&#x20;

CEIL : 어제했던 TRUNC이 자르는 역할, 즉 버림이였다면 ceil은 정수로 반올림해주는 함수인데 여기서 중요한건 소수점 첫째 자리에서 올림하는 함수로 원하는 자리수를 매길 수 없다는 단점이 있다

REPLACE : 특정 문자열을 치환하거나 제거하기 위해서 사용

* REPLACE(컬럼, 찾을문자, 변환할문자)



#### Top Earners

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





#### Weather Observation Station 2

{% embed url="https://www.hackerrank.com/challenges/weather-observation-station-2/problem?h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_r=next-challenge&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&h_v=zen&isFullScreen=true" %}

LAT\_N을 다 더하고 &#x20;













