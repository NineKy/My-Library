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











