# JPQL 2번째
<br><br>

## JPQL 조인
#### 내부조인
내부 조인은 INNER JOIN을 사용하는데, 여기서 INNER는 생략 가능하다
간단한 사용 예시로는
```Java
SELECT m
FROM Member m INNER JOIN m.team t
where t.name = :teamName
```

이렇게 사용할 수 있다
FROM Member m 별칭을 사용하면서 테이블을 선택하고
회원이 가지고 있는 team으로 팀과 함께 조인을 걸고 조인을 건 팀에는 t라는 별칭을 주어줌
<br><br>

#### 외부조인
사용 예시로는
```Java
SELECT m
FROM Member m LEFT OUTER JOIN m.team t
```
외부 조인은 기능상 SQL의 외부 조인과 같다고 볼 수 있다
추가적으로 OUTER도 생략이 가능하기 때문에 단순하게 LEFT JOIN으로 사용되곤 한다
<br><br>

#### 컬렉션 조인
컬렉션을 사용하는 곳을 키로 잡아서 조인하는 것을 컬렉션 조인이라고 한다

Member -> Team으로의 조인은 다대일 조인이면서 단일 값 연관 필드를 사용(Member.team)
Team -> Member으로의 조인은 일대다 조인이면서 컬렉션 값 연관 필드를 사용(Team.members)
예시로 보자
```Java
SELECT t, m FROM Team t LEFT JOIN t.members m
```
t LEFT JOIN t.members 이렇게 팀과 팀이 보유한 회원목록을 컬렉션 값 연관 필드로 외부 조인 했다
<br><br>

#### 세타 조인
WHERE절을 사용해서 세타 조인을 사용할 수 있고 -> 내부 조인만 지원
특징으로는 전혀 관계 없는 엔티티도 조인할 수 있다는 점이다
<br><br>

#### JOIN ON
조인을 걸 때 ON 절을 사용하게 되면 조인을 거는 대상을 필터링 하고 조인할 수 있다
참고로 내부 조인의 ON절은 where절을 사용할 때와 결과가 같으므로 보통 외부 조인에서만 사용
<br><br><br><br>

## fetch 조인
사용 방법은
페치 조인 ::= [ LEFT [OUTER] | INNER ] JOIN FETCH 조인 경로
<br>

#### 엔티티 페치 조인
fetch 조인은 조인의 종류라기 보다는 성능 최적화를 위해서 제공하는 기능이고
엔티티나 컬렉션을 한 번에 같이 조회하는 기능이다
사용 예시로 보자
```Java
String jpql = "select m from Member m join fetch m.team";
List<Member> members = em.createQuery(jpql, Member.class).getResultList();
members.forEach(member -> {
    //패치 조인으로 회원과 팀을 함께 조회해서 지연 로딩 발생 안함
    System.out.println("username = "+member.getUserName() + ", teamname = "+ member.getTeam().name());
})
```
join 다음에 fetch 이렇게 적고나면 연관된 엔티티나 컬렉션을 함께 조회하는데
여기서는 member 그리고 member.team을 함께 조회하게 된다.

만약에 회원과 팀을 지연로딩으로 설정해두었다면 회원을 조회하는데 있어서
페치 조인을 사용해서 팀도 함께 조회했기 때문에 연관된 팀 엔티티는 프록시가 아닌 실제 엔티티다
그렇기 때문에 지연 로딩이 일어나지 않으며 실제 엔티티이기 때문에 영속성 컨텍스트에서 분리되어도 연관된 팀을 조회할 수 있다
<br>

#### 컬렉션 페치 조인
일대다 관계인 페치 조인같은은 경우에는
```Java
String jpql = "select t from Team t join fetch t.members where t.name = '팀A'";
List<Team> teams = em.createQuery(jpql, Team.class).getResultList();

teams.forEach(team -> {
    System.out.println("teamname = " + team.getName() + ", team = "+team);
    team.getMembers.forEach(member -> {
        //페치 조인으로 팀과 회원을 함께 조회해서 지연 로딩 발생 안함
        System.out.println("->username = "+member.getUsername()+"member = "+member);
    })
})
```
컬렉션을 페치 조인한 select t으로만 팀을 조회했는데 수행된 SQL을 보면 팀과 연관된 회원도 함께 조회함
그럼 team 테이블에서 '팀A'는 하나이지만 member테이블과 조인하면서 결과가 증가해서 팀 A가 2번 조회됨
그렇기 때문에 같은 주소로 2번 조회되는 것을 알 수 있다
<br>

#### 페치 조인과 DISTINCT
우리가 알고 있는 SQL의 DISTINCT명령어와 같이 중복된 결과를 제거하는 명령
하지만 페치 조인에서 사용하는 것은 SQL에 DISTINCT를 추가하는 것 뿐만 아니라 애플리케이션에서도 중복을 제거
그러면 위에서처럼 2회 조회한 것을 DISTINCT를 통해서 애플리케이션에서 중복된 데이터를 제거할 수 있음
<br>

#### 페치 조인과 일반 조인의 차이
JPQL은 결과를 반환할 때 연관관계까지 고려하지 않기 때문에 단지 SELECT 절에 지정한 엔티티만을 조회할뿐이기 때문에
team만 조회하고 연관된 member는 조회하지 않는다
만약 회원에서 지연 로딩을 설정해두면 프록시나 아직 초기화하지 않은 컬렉션 래퍼를 반환하고 즉시 로딩으로 설정하면 회원 컬렉션을 즉시 로딩하기 위해 쿼리를 한 번 더 실행
<br>

#### 페치 조인의 특징과 한계
페치 조인을 사용하면 SQL 한 번으로 연관된 엔티티들을 함께 조회할 수 있어서 SQL 호출 횟수를 줄여서 성능을 최적화 가능
<br>
엔티티에 직접 적용하는 로딩 전략은 애플리케이션 전체에 영향을 미치기 때문에 글로벌 로딩 전략이라 부른다
<br>
장점
최적화를 위해 글로벌 로딩 전략을 즉시 로딩으로 설정하면 애플리케이션 전체에서 항상 즉시 로딩이 일어나게 된다
일부는 빠를 수 있지만 전체로 보면 사용하지 않는 엔티티를 자주 로딩하기 때문에 성능이 좋지 않음
따라서 글로벌 로딩 전략은 될 수 있다면 지연 로딩을 사용하고 최적화가 필요하면 페치 조인을 적용하는 것이 효과적
페치 조인을 사용하면 연관된 엔티티를 쿼리 시점에 조회하기 때문에 지연로딩이 발생x -> 준영속 상태에서도 객체 그래프 탐색 가능
<br>
단점
페치 조인 대상에게는 별칭을 줄 수 없음
둘 이상의 컬렉션을 페치할 수 없음
컬렉션을 페치 조인하면 페이징 API를 사용할 수 없음
<br>
페치 조인은 SQL 한 번으로 연관된 여러 엔티티를 조회할 수 있어서 성능최적화에 유용하지만
여러 테이블을 조인해서 엔티티가 가지고 있는 모양이 아닌 다른 모양으로 출력해야 한다면 굳이 패치 조인사용하지 않고 dto가 더 좋음
<br><br><br><br>

## 경로 표현식
경로 표현식은 쉽게 .을 통해서 객체 그래프를 탐색하는 것임
<br>
용어 정리
상태 필드 : 단순히 값을 저장하기 위한 필드
연관 필드 : 연관관계를 위한 필드, 임베디드 타입 포함
단일 값 연관 필드 : @ManyToOne, @OneToOne, 대상이 엔티티
컬렉션 값 연관 필드 : @OneToMany, @ManyToMany, 대상이 컬렉션
<br>

특징
JPQL에서 경로 표현식을 사용해서 경로 탐색을 하려면 다음 3가지 경로를 확인<br>
- 상태 필드 경로 : 경로 탐색의 끝 -> 더는 탐색 불가<br>
- 단일 값 연관 경로 : 묵시적으로 내부 조인이 일어나고 계속 탐색 가능<br>
- 컬렉션 값 연관 경로 : 묵시적으로 내부 조인이 일어나지만 더는 탐색 불가능<br>

예시
상태 필드 경로 탐색
```Java
select m.username, m.age from Member m
```
여기서 m.username, m.age는 상태 필드 경로 탐색이다
<br>
단일 값 연관 경로 탐색
```Java
select o.member from Order o
```
단순하게 이렇게 사용하게 되면 묵시적으로 내부 조인이 일어나게 된다. 항상 묵시적으로 일어나는 것은 내부 조인
그리고 명시적으로 JOIN을 적어주는 것도 가능은 하다
<br>
컬렉션 값 연관 경로 탐색
컬렉션까지의 경로 탐색을 어렵지 않게 가능하다. 하지만 컬렉션에서부터 경로 탐색을 하는 것은 불가능하다
만약에 컬렉션 내부에서 경로 탐색을 하고 싶다면 조인을 사용해서 새로운 별칭을 넣어준 다음부터 경로 탐색을 진행

묵시적 조인 시 주의 사항
항상 내부 조인이라는 점
컬렉션의 경로 탐색은 끝이다 -> 내부로 들어가고자 하면 명시적으로 조인을 걸어
경로 탐색은 SELECT, WHERE에서 사용하지만 묵시적 조인으로 인해서 FROM절에 영향을 준다
<br><br><br><br>

##서브 쿼리
서브 쿼리를 WHERE, HAVING절에서만 사용할 수 있고 SELECT, FROM 절에서는 사용할 수 없음
<br>
#### 서브 쿼리 함수
[NOT] EXISTS (subquery), {ALL | ANY | SOME} (subquery), [NOT] IN (subquery)
<br>
EXISTS
- 문법 : [NOT] EXISTS (subquery)
- 설명 : 서브쿼리에 결과가 존재하면 참이다. NOT은 반대
- 예시 :
  팀A의 소속인 회원
  select m from Member m where exists (select t from m.team t where t.name = '팀A')

{ALL | ANY | SOME}
- 문법 : {ALL | ANY | SOME} (subquery)
- 설명 : 비교 연산자와 같이 사용
  - ALL : 조건을 모두 만족하면 참
  - ANY, SOME : 둘은 같은 의미로 하나라도 만족하면 참
- 예시 :
  전체 상품 각각의 재고보다 주문량이 많은 주문들
  select o from Order o where o.orderAmount > ALL (select p.stockAmount from Product p)
  어떤 팀이든 팀에 소속된 회원
  select m from Member m where m.team = ANY(select t from Team t)

IN
- 문법 : [NOT] IN (subquery)
- 설명 : 서브 쿼리의 결과 중 하나라도 같은 것이 있으면 참
- 예시 :
  20세 이상을 보유한 팀
  select t from Team t where t IN (select t2 from team t2 join t2.members m2 where m2.age >= 20)

<br><br><br>

## 조건식
####타입 표현
문자 - ''사이에 표현, '을 표현하고 싶다면 ''이렇게 2개로 ㅅ작성
숫자 - Long -> L, Double -> D, Float -> F
날짜 - DATE -> {d 'yyyy-mm-dd'}, TIME -> {t 'hh-mm-ss'}, DATETIME -> {ts 'yyyy-mm-dd hh:mm:ss.f'}
Boolean - TRUE, FALSE
Enum - 패키지명을 포함한 전체 이름을 사용해야 함 OrderStatus.OK
엔티티 타입 - 엔티티의 타입을 표현한다. 주로 상속과 관련해서 사용
<br>

#### 연산자 우선순위
연산자의 우선순위는
1. 경로 탐색 연산(.)
2. 수학연산
3. 비교연산
4. 논리연산
<br>

#### 논리 연산과 비교식
논리 연산
- AND: 둘다 만족하면 참
- OR: 둘 중 하나만 만족해도 참
- NOT: 조건식의 결과 반대

비교식
= | > | >= | < | <= | <>
<br>

#### Between, IN, Like, NULL 비교
Between 식
- 문법 : X [NOT] BETWEEN A AND B
- 설명 : X는 A ~ B 사이의 값이면 참
- 예시 :
  나이가 10~20인 회원을 찾자
  select m from Member m where m.age between 10 and 20

IN 식
- 문법 : X [NOT] IN
- 설명 : X와 같은 값이 예제에 하나라도 있으면 참이다. IN 식의 예제는 서브 쿼리에서도 사용 가능
- 예시 :
  이름이 회원1이나 회원2인 회원을 찾자
  select m from Member m where m.username in ('회원1', '회원2')

Like 식
- 문법 : X [NOT] LIKE
- 설명 : 문자표현식과 패턴 값을 비교

NULL 비교식
- 문법 : {단일값 경로 | 입력 파라미터} IS [NOT] NULL
- 설명 : NULL인지 비교한다. NULL은 =으로 비교하면 안 되고 꼭 IS NULL을 사용해야 함
<br>

#### 컬렉션 식
컬렉션은 특별해서 컬렉션 전용 식만 사용이 가능함

빈 컬렉션 비교 식
- 문법 : {컬렉션 값 연관 경로} IS [NOT] EMPTY
- 설명 : 컬렉션에 값이 비었으면 참

컬렉션의 멤버 식
- 문법 : {엔티티나 값} [NOT] MEMBER [OF] {컬렉션 값 연관 경로}
- 설명 : 엔티티나 값이 컬렉션에 포함되어 있으면 참

#### 스칼라 식
스칼라 식이라는 숫자, 문자, 날짜, case, 엔티티 타입과 같은 기본적인 타입들
수학 식
- +, - : 단항 연산자
- *, /, +, - : 사칙 연산

문자함수
- CONCAT
- SUBSTRING
- TRIM
- LOWER
- UPPER
- LENGTH
- LOCATE

수학함수
- ABS
- SQRT
- MOD
- SIZE
- INDEX

날짜함수
- CURRENT_DATE
- CURRENT_TIME
- CURRENT_TIMESTAMPE
이건 살짝 방언바이라서... 필요할 떄

#### CASE
크게 4가지가 존재
기본 CASE
```SQL
CASE
    {WHEN <조건식> THEN <스칼라식>}
    ELSE <스칼라식>
END
```

심플 CASE
```SQL
CASE <조건대상>
    {WHEN <스칼라식1> THEN <스칼라식2>}
    ELSE <스칼라식>
END
```

COALESCE
-문법 : COALESCE(<스칼라식> {, <스칼라식>}+)
-설명 : 스칼라식을 차례대로 조회해서 null이 아니면 반환

NULLIF
-문법 : NULLIF(<스칼라식>, <스칼라식>)
-설명 : 두 값이 같으면 null을 반환하고 다르면 첫 번째 값을 반환한다. 집합 함수는 null을 포함하지 않으므로 보통 집합 함수와 같이씀

<br><br><br><br><br><br>
