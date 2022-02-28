# Advanced Select

#### Type of Triangle

[https://www.hackerrank.com/challenges/what-type-of-triangle/problem?isFullScreen=true](https://www.hackerrank.com/challenges/what-type-of-triangle/problem?isFullScreen=true)

Write a query identifying the _type_ of each record in the **TRIANGLES** table using its three side lengths. Output one of the following statements for each record in the table:

* **Equilateral**: It's a triangle with  sides of equal length.
* **Isosceles**: It's a triangle with  sides of equal length.
* **Scalene**: It's a triangle with  sides of differing lengths.
* **Not A Triangle**: The given values of _A_, _B_, and _C_ don't form a triangle.

**Input Format**

The **TRIANGLES** table is described as follows:

![](https://s3.amazonaws.com/hr-challenge-images/12887/1443815629-ac2a843fb7-1.png)

Each row in the table denotes the lengths of each of a triangle's three sides.

```sql
SELECT
    CASE 
    WHEN A+B<=C OR A+C<=C OR B+C<=A THEN 'Not A Triangle'
    WHEN A=B AND B=C AND A=C THEN 'Equilateral'
    WHEN A=B OR B=C OR A=C THEN 'Isosceles'
    ELSE 'Scalene'
    END
FROM TRIANGLES;
```

SQL에서의 분기문을 치는 방법은&#x20;

CASE, WHEN-THEN, ELSE, END

WHEN에는 조건값을, THEN에는 결과값을 넣어주면되는데, 리턴값에는 null값이 들어갈 수 없다는 점 기억

기본적으로 if-else의 방식이라고 생각하는데, switch문 처럼 사용하고 싶다면

CASE 뒤에 switch에서 검사할 항목이 무엇인지 적어주고, WHEN에는 그냥 항목에 대한 값을 적어주면 된다





#### The PADS

[https://www.hackerrank.com/challenges/the-pads/problem?isFullScreen=true\&h\_r=next-challenge\&h\_v=zen](https://www.hackerrank.com/challenges/the-pads/problem?isFullScreen=true\&h\_r=next-challenge\&h\_v=zen)

뭔가 영어를 너무 쏼라쏼라 여서... 번역기를 사용해서 진행

1. OCCUPATIONS의 모든 이름을 알파벳 순으로 나열하고, 나열할 때 각 직업의 첫 글자를 괄호로 묶어서 출력하라
2. OCCUPATIONS에서 나온 각 직업의 갯수를 'There are a total of \[숫자] \[직업]s' 이 포맷으로 출력하라

```sql
SELECT NAME || '(' || SUBSTR(OCCUPATION, 1, 1) || ')'
FROM OCCUPATIONS
ORDER BY NAME;

SELECT 'There are a total of ' || COUNT(OCCUPATION) || ' ' || LOWER(OCCUPATION) || 's.'
FROM OCCUPATIONS
GROUP BY OCCUPATION
ORDER BY COUNT(NAME), OCCUPATION;
```

막혔던 부분은 SELECT 문에서 문자열을 더하는 것이였는데...&#x20;

SQL에서는 문자열 + 은 ||로 표현한다는 것을 알게되었다...!

COUNT, UPPER, LOWER, SUBSTR는 기존에 했던게 기억나더라









####

