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









