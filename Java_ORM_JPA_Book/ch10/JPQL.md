# JPQL

이전의 다양한 것들을 봤겠지만 어떤 방식을 사용하든 항상 JPQL이 먼저로 시작되게 된다<br>
일단 한번 JPQL의 특징을 정리해보자
<br>

* JPQL은 객체지향 쿼리 언어이다. 따라서 테이블을 대상으로 쿼리하는 것이 아니라 **엔티티 객체를 대상으로** 쿼리
* JPQL은 SQL을 추상화해서 특정 데이터베이스 SQL에 의존하지 않음
* JPQL도 결국은 SQL로 반환된다는 점

<br><br><br>

## 기본 문법과 쿼리 API

JPQL도 SQL과 비슷하게 select, update, delete문을 사용하는 것이 가능하다<br>
:근데 insert는 왜 없냐? -> EntityManager.persist()로 저장하면 되니까
<br>

### SELECT 문

예시를 봐볼까<br>

```sql
SELECT m FROM Member AS m where m.username = 'Hello'
```

특징별로 보자

#### 대소문자 구분

엔티티의 속성은 대소문자를 구분 -> Member, username은 대소문자를 구분하고 select, from as 와 같은 JPQL 용어는 대소문자를 구분하지 않음
<br>

#### 엔티티 이름

JPQL에서 사용한 Member는 클래스 명이 아니라 엔티티 명이다. 엔티티 명을 따로 지정하지 않는 이상은 클래스명을 기본적으로 엔티티명으로 처리<br>
그래서 기본값인 클래스 명을 엔티티 명으로 사용하는 것을 추천
<br>

#### 별칭은 필수

굳이 as을 사용해서 별칭을 사용한 것으로 보이는데, JPQL은 별칭을 필수로 사용해주어야 한다.
<br><br><br>

## TypeQuery, Query

작성한 JPQL을 실행하기 위해서는 쿼리 객체를 만들어야 한다. 쿼리 객체로 존재하는 것이 TypeQuery, Query이렇게 존재하는데 <br>
차이점으로는 반환할 타입이 있다면 TypeQuery, 반환할 타입을 지정할 수 없다면 Query 이렇게 나누어 진다<br>
<br>
TypeQuery, Query 사용기<br>

```java
TypedQuery<Member> query = em.createQuery("SELECT m FROM Member m", Member.class);

List<Member> resultList = query.getResultList();
for(Member member : resultList){
    System.out.println("member = "+member);
}

---------------------------------------------------------------------------------------------------------

Query query = em.createQuery("SELECT m.username, m.age from Member m");
List resultList = query.getResultList();

for(Object o : resultList){
    Object[] result = (Object[]) o;
    System.out.println("username = " + result[0]);
    System.out.println("age = " + result[1]);
}
```

첫 번째는 리턴될 타입을 미리 알고 있었기 때문에 조회한 쿼리를 사용하는 것도 어렵지 않게 사용하는 반면
두 번째는 반환할 타입이 애매하다 보니까 조죄하는 것도 어렵게 조회하는 모습이다...
그래 굳이 찾아서 또 타입 변환하고 있지 말고 그냥 애초부터 정해진 탑으로 빼버리자....<br>
<br>
결과를 조회하는 방법으로는

1. query.getResultList() : 결과를 예제로 반환하고 만약에 결과가 없으면 빈 컬렉션을 반환
2. query.getSingleResult() : 결과가 정확히 하나일 때 사용함 - 결과가 없는 경우에는 NoResultException을 던지고 결과가 하나보다 많으면 NonUniqueResultException을 던짐

<br><br><br>

## 파라미터 바인딩

JDBC에서는 위치 기준 파라미터 바인딩만을 지원하고 있지만 JPQL은 이름 기준 파라미터 바인딩도 지원함<br>

#### 이름 기준 파라미터

파라미터를 이름을 기준으로 구분하는 방법이고, 이름 기준 파라미터 앞에는 :을 사용한다

```java
String usernameParam = "User1";
TypedQuery<Member> query = em.createQuery("SELCT m FROM Member m WHERE m.username = :username",
Member.class);

query.setParameter("username", usernameParam);
List<Member> resultList = query.getResultList();
```

:username 이렇게 사용해주고, query.setParameter(이름, 값) 이렇게 파라미터에 값을 바인딩해주자
<br><br>

#### 위치 기준 파라미터

```java
List<Member> members = em.createQuery("select m from Member m where m.username = ?1", Member.class)
      .setParameter(1, usernameParam)
      .getResultList();
```

위치 기반 파라미터는 ?(물음표) 다음에 위치 값을 주는 걸로 시작한다
그리고 query.setParameter(위치, 값) 으로 값을 바인딩해줌<br>
<br><br><br>

## 프로젝션

프로젝션이란? select 절에 조회할 대상을 지정하는 것을 의미함
SELECT 프로젝션대상 FROM 이렇게 대상을 지정하는데 타입으로는 다양하게 있다
<br>

#### 엔티티 프로젝션

```sql
SELECT m FROM Member m
SELECT m.team FROM Member m
```

위 쿼리문 2개 다 엔티티를 프로젝션의 대상으로 정해서 객체를 바로 조회했다 이건 하나하나 조회하는 SQL과는 차이가 있음
이렇게 조회한 엔티티는 영속성 컨텍스트에서 관리됨
<br><br>

#### 임베디드 타입 프로젝션

엔티티와 비슷하지만 역시 임베디드 타입이기 때문에 조회의 시작점이 될 수 없다는 제약이 존재한다.

```java
String query = "select o.address from Order o";
List<Address> addresses = em.createQuery(query, Address.class).getResultList();
```

임베디드 타입은 엔티티 타입이 아닌 값 타입이다. 따라서 이렇게 직접 조회한 임베디드 타입은 영속성 컨텍스트에서 관리되지 않는다
<br><br>

#### 스칼라 타입 프로젝션

숫자, 문자, 날짜와 같은 기본 데이터 타입들을 스칼라 타입이라고 정의한다.
예로 전체 회원의 이름을 조회하기 위해서는

```java
List<String> query = em.createQuery("SELECT username FROM Member", String.class).getResultList();
```

<br><br>

#### 여러 값 조회

만약에 프로젝션에서 여러 값들을 선택하게 되는 순간, 위에서 사용했던 TypeQuery는 사용할 수 없고 단순한 Query를 사용해야 한다.
예제로 확인

```java
Query query = em.createQuery("SELECT m.username, m.age FROM Member m");
List resultList = query.getResultList();

Iterator iterator = resultList.iterator();
while(iterator.hasNext()){
    Object[] row = (Object[]) iterator.next();
    String username = (String) row[0];
    Integer age = (Integer) row[1];
}
```
<br>
이렇게 뿐만 아니라 엔티티 타입도 여러 값을 함께 조회할 수 있음

```java
List<Object[]> resultList =
    em.createQuery("SELECT o.member, o.product, o.orderAmount FROM Order o").getResultList();

for(Object[] row : resultList){
  Member member = (Member) row[0];
  Product product = (Product) row[1];
  int orderAmount = (Integer) row[2];
}
```
<br><br>
**NEW 명령어**
위의 경우에는 모든 result를 받아주는 객체가 존재하지 않았기 때문에 저렇게 Object[]으로 리턴받아서 각각 매핑해주는 과정을 거쳤는데,
이러한 방법을 DTO를 생성함으로써 의미 있는 객체로 만들어서 받을 수 있다..!
대신 DTO를 생성하는데 있어서 -> AllArgsConstructer는 필수로 필요하다는점

```java
TypeQuery<UserDTO> query = em.createQuery("SELCT new jpabook.jpql.UserDTO(m.username, m.age) FROM Member m", UserDTO.class);
List<UserDTO> resultList = query.getResultList();
```

new 키워드를 사용해서 정--말 귀찮은 객체변환 작업을 손쉽게 처리할 수 있다는 점이 좋네요!<br>
그래도 주의할 점 2가지
1. 패키지 명을 포함한 전체 클래스 명을 입력해야 한다는 점!
2. 순서와 타입이 일치하는 생성자가 필요하다는 점!
<br><br><br>


## 페이징 API
페이징 처리용 SQL을 작성하는 일은 정말 매우 지루한 점이다. 그리고 가장 큰 문제는 데이터베이스마다 페이징 처리하는 쿼리가 다르다는 점
그래서 JPA에서는 2개의 API로 추상화 해두었다
- setFirstResult(int startPosition) : 조회 시작 위치
- setMaxResult(int maxResult) : 조회할 데이터 수

```java
TypeQuery<Member> query = em.createQuery("SELECT m FROM Member m ORDER BY m.username DESC", Member.class);
query.setFirstResult(10);
query.setMaxResult(20);
query.getResultList();
```
위의 코드는 10이 시작이기 때문에
11부터 20개를 조회한다.
즉 11 ~ 30번의 데이터를 조회한다.
<br>
만약 더 추가적으로 페이징 API를 최적화시키고 싶다면 네이티브로 다시 쿼리를 짜야한다는 점을 기억하고 있자...!
<br><br><br>

## 집합과 정렬
집합은 집합함수와 함께 통계 정보를 구할 때 사용함
<br>
#### 집합 함수
- COUNT : 결과 수를 구한다 -> 반환 값 Long
- MAX, MIN : 최대, 최소 값을 구한다 -> 문자, 숫자, 날짜 등에 사용
- AVG : 평균값을 구하고 숫자 타입에만 사용할 수 있음 -> 반환 값 double
- SUM : 합을 구하고 숫자 타입에만 사용할 수 있음 -> 각 더한 타입에 맞는 반환 값을 반환함


<br><br>
**주의할점**
- null 값은 무시되고 집계됨
- 값이 없는데 min, max, avg, sum을 때리면 null이 되지만 count는 0이 됨
- distinct는 중복된 값을 제거하고 나서 출력됨
- distinct는 count를 사용할 때 임베디드 타입을 지원하지 않음


<br><br>
#### GROUP BY, HAVING
GROUP BY 는 통계 데이터를 구할 때 특정 그룹끼리 묶는 예약어이다.
HAVING은 GROUP BY와 함께 사용하는데 GROUP BY로 그룹화한 통계 데이터를 기준으로 필터링
<br>
사용하는 문법은 이러하다
```java
groupby_절 :: = GROUP BY {단일값 경로 | 별칭 }+
having_절 :: = HAVING 조건식
```
<br>
이러한 쿼리들은 보통 통계(reporting)쿼리라고 한다
아주 유용하다고 볼 수 있지만 이미 존재하는 데이터를 기준으로 처리하는 쿼리이기 때문에 실시간으로 사용하기엔 어려움
아주 많은 데이터들을 통계처리 하기에는 통계 결과만 저장하는 테이블을 별도로 만들어 두고 사용자가 적은 새벽에 돌려서 결과를 저장하는 방안이 베스트

<br>
#### ORDER BY
ORDER BY는 결과를 내림차순이나, 오름차순으로 정리해주는 방법
```java
orderby_절 ::= ORDER BY {상태필드 경로 | 결과 변수 [ASC | DESC]}+
```
ASC : 오름차순(기본값 - 따로 설정안해도 알아서 오름차순)
DESC : 내림차순
