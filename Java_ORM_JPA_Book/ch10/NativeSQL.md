# 네이티브 SQL
jpql은 표준 SQL이 지원하는 대부분의 문법과 SQL함수들을 지원하지만, 특정 데이터베이스에 종속적인 기능은 지원하지 않음 <br>
예를 들면 <br> 
특정 데이터베이스만 지원하는 함수, 문법, SQL쿼리 힌트 <br>
인라인 뷰, union, intersect <br>
스토어드 프로시저 <br>
등등이 있다 <br>
네이티브 SQL을 사용하는 이유는 JPQL을 사용할 수 없는 경우에서 직접 SQL을 사용하기 위해서 사용한다 <br>
JPQL을 사용하면 JPA가 SQL을 생성하는데, 네이티브SQL은 개발자가 직접 정의한다는 점 <br><br>
그럼 jdbc의 api를 사용하는 것과 jpa를 사용하는 것은 어떤 차이가 있냐? <br>
-> 네이티브SQL을 사용한다면 엔티티를 조회하는 것이 가능하고 JPA가 지원하는 영속성 컨텍스트의 기능을 그대로 사용 <br>
-> JDBC API를 직접 사용하면 단순하게 데이터를 나열 <br>
<br>
<br><br>

##네이티브 SQL의 사용
네이티브 쿼리 api에는 3가지가 존재
```java
//결과 타입을 정의
public Query createNativeQuery(String sqlString, Class resultClass);

//결과 타입을 정의할 수 없을 때
public Query createNativeQuery(String sqlString);

public Query createNativeQuery(String sqlString, String resultSetMapping);
```
<br>

#### 엔티티 조회
뭐 그렇게 다른점은 없다..! <br>
네이티브 SQL로 SQL만 직접 사용할 뿐이지 나머지는 JPQL을 사용할 때와 같으며 조회한 엔티티도 영속성 컨텍스트로 관리된다 <br>
```java
String sql = "SELECT ID, AGE, NAME, TEAM_ID FROM MEMBER WHERE AGE > ?";

Query nativeQuery = em.createNativeQuery(sql, Member.class).setParameter(1, 20);

List<Member> resultList = nativeQuery.getResultList();
```
<br><br>

#### 값 조회
단순하게 값으로만도 조회하는 것이 가능하다
```java
String sql = "SELECT ID, AGE, NAME, TEAM_ID FROM MEMBER WHERE AGE > ?";

Query nativeQuery = em.createNativeQuery(sql).setParameter(1, 10); 

List<Object[]> resultList = nativeQuery.getResultList();
for(Object[] row : resultList){
    System.out.println("id = " + row[0]);
    System.out.println("age = " + row[1]);
    System.out.println("name = " + row[2]);
    System.out.println("team_id = " + row[3]);
}
```
엔티티로 조회하는 것이 아니라 단순하게 값으로 조회하기 위해서 class를 두 번쨰 파라미터에 설정해주지 않은 모습이고 <br>
조회된 값을 엔티티가 아닌 값에다가 넣을 것이기 때문에 Object[]에 담아서 반환했다 <br>
여기서는 스칼라 값들을 조회했을 뿐이기 때문에 결과를 영속성 컨텍스트가 관리하지 않는다는 점!! <br>
<br>

#### 결과 매핑 사용
만약에 엔티티와 스칼라 값을 함께 조회하는 것 처럼 매핑이 복잡해지면 @SqlResultSetMapping을 정의해서 결과 매핑을 사용해야 함 <br>
사용하는 예시를 보자 <br>
```java
String sql = "SELECT M.ID, AGE, NAME, TEAM_ID, I.ORDER_COUNT FROM MEMBER M" +
            "LEFT JOIN " +
            "(SELECT IM.ID, COUNT(*) AS ORDER_COUNT " +
            "FROM ORDERS O, MEMBER IM " +
            "WHERE O.MEMBER_ID = IM.ID) I " +
            "ON M.ID = I.ID";

Query nativeQuery = em.createNativeQuery(sql, "memberWithOrderCount");

List<Object[]> resultList = nativeQuery.getResultList();
for(object[] row : resultList){
    Member member = (Member) row[0];
    BigInteger orderCount = (BigInteger)row[1];
    
    System.out.println("member = " + member);
    System.out.println("orderCount = " + orderCount);
}
```
이렇게 두 번째 파라미터에 결과 매핑 정보의 이름이 사용되었음 <br>
이렇게 결과 매핑을 해볼 수 있다는 점이다 <br>
```java
@Entity
@SqlResultSetMapping(name = "memberWithOrderCount",
    entities = {@EntityResult(entityClass = Member.class),
    columns = {@ColumnResult(name = "ORDER_COUNT")}
public class Member{ ...}
```
memberWithOrderCount의 결과 매핑은 Member 엔티티와 ORDER_COUNT 칼럼을 매핑했는데, <br>
매핑하는 것은 다수의 엔티티와 다수의 컬럼을 이어주는 것이 가능하다 <br>
@EntityResult 이외에도 @FieldResult 라는 애노테이션을 사용해서 컬럼명과 필드명을 직접 매핑하는 것도 가능 <br>
<br><br>

#### 결과 매핑 애노테이션
@SqlResultSetMapping
- name : 결과 매핑 이름
- entities : @EntityResult를 사용해서 엔티티를 결과로 매핑
- columns : @ColumnResult를 사용해서 컬럼을 결과로 매핑

<br>

@EntityResult
- entityClass : 결과로 사용할 엔티티 클래스를 지정
- fields : @FieldResult을 사용해서 결과 컬럼을 필드와 매핑
- discriminatorColumn : 엔티티의 인스턴스 타입을 구분하는 필드(상속할 때 사용)

<br>

@FieldResult
- name : 결과를 받을 필드명
- column : 결과 컬럼명

<br>

@ColumnResult
- name : 결과 컬럼명

<br><br><br>

## Named 네이티브 SQL
JPQL에서 처럼 네이티브SQL도 Named 네이티브 SQL을 사용해서 정적 SQL을 작성할 수 있다 <br>
```java
@Entity
@NamedNativeQuery(
    name = "Member.memberSQL",
    query = "SELECT ID, AGE, NAME, TEAM_ID FROM MEMBER WHERE AGE > ?", resultClass = Member.class
)
public class Member { ... }
```

@NamedNativeQuery 애노테이션을 사용해서 Named네이티브 쿼리를 등록하고 사용은 이렇게  <br>

```java
TypedQuery<Member> nativeQuery = em.createQuery("Member.memberSQL", Member.class).setParameter(1, 20);
```

사용뿐만 아니라 결과 매핑 자체는 <br>

```java
@Entity
@SqlResultSetMapping(name = "memberWithOrderCount",
    entities = {@EntityResult(entityClass = Member.class)},
    columns = {@ColumnResult(name = "ORDER_COUNT")}
)
@NamedNativeQuery(
    name = "Member.memberWithOrderCount",
    query = "SELECT M.ID, NAME, TEAM_ID, I.ORDER_COUNT FROM MEMBER M" +
            "LEFT JOIN (SELECT IM.ID, COUNT(*) AS ORDER_COUNT " +
                        "FROM ORDERS O, MEMBER IM " +
                        "WHERE O.MEMBER_ID = IM.ID) I " +
            "ON M.ID = I.ID",
            resultSetMapping = "memberWithOrderCount"
)
public class Member {...}
```
Named 네이티브 쿼리에서 조회 결과를 매핑까지 memberWithOrderCount에 맞춰줬고
```java
List<Object[]> resultList = em.createNamedQuery("Member.memberWithOrderCount").getResultList();
```
이렇게 사용한다 <br>
<br>

#### @NamedNativeQuery
- name : 네임드 쿼리 이름(required)
- query : SQL 쿼리(required)
- hints : 벤더 종속적인 힌트
- resultClass : 결과 클래스
- resultSetMapping : 결과 매핑 사용

#### 네이티브 SQL 정리
네이티브 SQL은 JPQL이 자동으로 생성하는 SQL을 수동으로 직접 정의하는 것이 아니기 때문에 JPA가 제공하는 기능 대부분 그대로 사용 가능 <br>
근데 이렇게 날 것으로 사용하는 것은 데이터베이스 간의 이식성도 떨어지고 종속적이라는 점..! <br>
<br><br><br>

## 스토어드 프로시저(Stored Procedure)
개념으로는 일련의 쿼리를 마치 하나의 함수처럼 실행 하기 위한 쿼리의 집합이다 <br>
<br>

예시로는
입력값을 2배로 만들어주는 proc_multiply가 있는데 <br>
jpa를 호출하는건
```java
StoredProcedureQuery spq = em.createStoredProcedureQuery("proc_multiply");
spq.registerStoredProcedureParameter(1, Integer.class, ParameterMode.IN);
spq.registerStoredProcedureParameter(2, Integer.class, ParameterMode.OUT);

spq.setParameter(1, 100);
spq.exeute();

Integer out = (Integer)spq.getOutputParameterValue(2);
System.out.println("out = " + out); 
```
이렇게 사용하면 out은 200 <br>

뭐 대충 이런 개념에 이렇게 사용한다고만 알아두고 눈에좀 보이면 다시 공부하자
<br>





<br><br><br><br><br><br><br><br><br><br>



















