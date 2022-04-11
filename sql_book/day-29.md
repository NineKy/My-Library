# 관계형 모델
SQL은 관계형 모델에 의해 구축된 데이터베이스를 조작하는 체계적인 명령의 집합이다 <br>
<br><br><br>

## 관계형 모델(Relational Model)
관계형 모델의 기본적인 요소는 릴레이션, 관계이다 <br>
기본적으로 일반적인 시스템의 데이터베이스에는 복수의 테이블이 존재하기 때문에 테이블 간의 관계가 매우 중요하다 <br>
관계형 모델의 릴레이션은 SQL에서 말하는 테이블에 해당된다 <br>
<br><br>

관계형 모델의 릴레이션에는 몇 가지 속성(Attribute)이 있고 그 속성에는 속성의 이름과 형 이름으로 구성된다 <br>
그리고 SQL에서의 행은 관계형 모델에서 tuple이라고 불리운다 <br>
릴레이션에 대한 연산이 집합의 대한 연산에 대응된다는 이론을 **관계대수**라고 하고 관계대수의 기본 규칙은 이렇다 <br>
- 하나 이상의 관계를 바탕으로 연산한다
- 연산한 결과, 반환되는 것 또한 관계이다
- 연산을 중첩 구조로 실행해도 상관없다

<br><br><br>

## 관계형 모델과 SQL
테이블 A와 테이블 B가 존재한다고 가정하고 보자 <br>
<br>

합집합 <br>
합집합(union)은 릴레이션끼리의 덧셈을 의미하고 SQL에서는 UNION에 해당함
```sql
SELECT * FROM A UNION SELECT * FROM B
```
<br><br>

차집합 <br>
차집합(difference)은 릴레이션끼리의 뺄셈을 의미하고 SQL에서는 EXCEPT에 해당함
```sql
SELECT * FROM A EXCEPT SELECT * FROM B
```
<br><br>

교집합 <br>
교집합(intersection)은 릴레이션끼리의 공통부분을 의미하고 SQL에서는 INTERSECT에 해당함
```sql
SELECT * FROM A INTERSECT SELECT * FROM B
```
<br><br>

곱집합 <br>
곱집합은 이전에 봤던 카티션 프로덕트에 해당하고 CROSS JOIN으로 교차결합을 통해서 곱집합을 구해낼 수 있음 <br>
```sql
SELECT * FROM A, B SELECT * FROM A CROSS JOIN B
```
<br><br>

선택 <br>
SELECT은 튜플의 추출을 의미하고 선택의 제한이라고도 한다 -> 여기서 WHERE 구를 통해서 조건을 지정해 데이터를 검색 <br>
```sql
SELECT * FROM A WHERE no < 3
```
<br><br>

투영 <br>
투영(projection)은 속성의 추출을 의미하고 SQL에서 속성은 열을 의미하기 때문에 SELECT 구에 결과로 반환된 열을 지정하는 것에 해당 <br>
```sql
SELECT a FROM A
```
<br><br>

결합 <br>
결합(join)은 릴레이션끼리 교차결합해 계산된 곱집합에서 결합조건을 만족하는 튜플을 추출하는 연산이고 SQL에서는 내부결합에 해당된다 <br>
```sql
SELECT a FROM A INNER JOIN B ON A.no=B.no
```
<br><br>


<br><br><br><br><br><br><br><br><br><br>