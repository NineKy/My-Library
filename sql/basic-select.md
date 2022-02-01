# Basic Select



#### Higher Than 75 Marks

[https://www.hackerrank.com/challenges/more-than-75-marks/problem?isFullScreen=true\&h\_r=next-challenge\&h\_v=zen\&h\_r=next-challenge\&h\_v=zen\&h\_r=next-challenge\&h\_v=zen](https://www.hackerrank.com/challenges/more-than-75-marks/problem?isFullScreen=true\&h\_r=next-challenge\&h\_v=zen\&h\_r=next-challenge\&h\_v=zen\&h\_r=next-challenge\&h\_v=zen)

The **STUDENTS** table is described as follows: ![](https://s3.amazonaws.com/hr-challenge-images/12896/1443815243-94b941f556-1.png)&#x20;

The _Name_ column only contains uppercase (`A`-`Z`) and lowercase (`a`-`z`) letters.

Query the _Name_ of any student in **STUDENTS** who scored higher than  _Marks_. Order your output by the _last three characters_ of each name. If two or more students both have names ending in the same last three characters (i.e.: Bobby, Robby, etc.), secondary sort them by ascending _ID_.

```sql
SELECT NAME
FROM STUDENTS
WHERE MARKS>75
ORDER BY SUBSTR(NAME, LENGTH(NAME)-2, 3), ID;
##위에서 사용해봤던 substr을 사용해서 처음에는 접근
------------------------------------------------------------
SELECT NAME FROM STUDENTS
WHERE MARKS>75
ORDER BY SUBSTR(NAME, -3), ID;
```

Oracle에서 사용할 수 있는 SUBSTR 함수에 대한 사용법

1. SUBSTR(문자열, 시작위치) : 앞을 기준으로 시작위치부터 끝까지 자르기
2. SUBSTR(문자열, 시작위치, 길이) : 앞을 기준으로  시작위치부터 길이만큼 자르기
3. SUBSTR(문자열, 시작위치(음수)) : 뒤를 기준으로 시작위치부터 끝까지 자르기
4. SUBSTR(문자열, 시작위치(음수), 길이) : 뒤를 기준으로 시작위치부터 길이만큼 자르기





#### Employee Names

[https://www.hackerrank.com/challenges/name-of-employees/problem?isFullScreen=true\&h\_r=next-challenge\&h\_v=zen\&h\_r=next-challenge\&h\_v=zen\&h\_r=next-challenge\&h\_v=zen\&h\_r=next-challenge\&h\_v=zen](https://www.hackerrank.com/challenges/name-of-employees/problem?isFullScreen=true\&h\_r=next-challenge\&h\_v=zen\&h\_r=next-challenge\&h\_v=zen\&h\_r=next-challenge\&h\_v=zen\&h\_r=next-challenge\&h\_v=zen)

Write a query that prints a list of employee names (i.e.: the _name_ attribute) from the **Employee** table in alphabetical order.

**Input Format**

The **Employee** table containing employee data for a company is described as follows:&#x20;

![](https://s3.amazonaws.com/hr-challenge-images/19629/1458557872-4396838885-ScreenShot2016-03-21at4.27.13PM.png)

where _employee\_id_ is an employee's ID number, _name_ is their name, _months_ is the total number of months they've been working for the company, and _salary_ is their monthly salary.

```sql
SELECT name
FROM employee
ORDER BY name;
```

네....





#### Employee Salaries

Write a query that prints a list of employee names (i.e.: the _name_ attribute) for employees in **Employee** having a salary greater than  2000$ per month who have been employees for less than  10 months. Sort your result by ascending _employee\_id_.

```sql
SELECT NAME
FROM (
    SELECT *
    FROM EMPLOYEE
    WHERE SALARY > 2000
)
WHERE MONTHS<10
ORDER BY EMPLOYEE_ID;

-----------------------------------------
SELECT NAME
FROM EMPLOYEE
WHERE SALRAY>2000 AND MONTHS<10
ORDER BY EMPLOYEE_ID;
```

GOOD!





