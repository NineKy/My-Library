# JPQL part3

\
\


## 다형성 쿼리

JPQL로 부모 엔티티를 조회하면 자식 엔티티도 함게 조회하게 된다\


다양한 타입이 존재하고 크게로는 TYPE - 엔티티의 상속 수조에서 조회 대상을 특정 자식 타입으로 한정할 때 주로 사용 TREAT - 상속 구조에서 부모 타입을 특정 자식 타입으로 다룰 때 사용\


\
\


## 사용자 정의 함수 호출

문법

```
function_invocation::=FUNCTION(function_name {, function_arg}*)
```

하이버네이트 구현체를 사용하기 위해서는 방언 클래스를 상속해서 구현하고 사용할 함수를 미리 등록해야함\


\
\


## 기타 정리

enum은 = 비교연산만 지원\
임베디드 타입은 비교를 지원하지 않음\
\


#### Empty String

JPA표준에서는 ''을 길이 0인 Empty String으로 지정해두었지만 데이터베이스마다 ''을 null로 간주하는 데이터베이스도 존재\


#### Null 정의

조건을 만족하는 데이터가 하나도 없으면 null null값의 의미는 unknownvalue이다 -> 따라서 null과 이루어지는 모든 수학적 계산 결과는 null null == null 은 알 수 없는 값이다 null IS NULL 은 true이다\


\
\


## 엔티티 직접 사용

#### 기본 키 값

객체 인스턴스는 참조 값으로 식별하고 테이블 로우는 기본 키값으로 식별한다\
따라서 JPQL에서 엔티티 객체를 직접 사용하면 SQL에서는 해당 엔티티의 기본 키 값을 사용\


```
select count(m.id) from Member m;
select count(m) from Member m;
```

첫 번째 줄은 m.id를 기준(기본키)으로 검색하는거지만\
두 번쨰 줄은 m를 기준(엔티티)으로 검색하는 건데,\
사실 실제로 sql문이 실행된 것을 확인해보면 둘 다 모두 엔티티의 기본 키를 사용한 것으로 확인할 수 있다\


사용하는 것을 코드로 보면\
엔티티를 직접 사용하는 코드

```
String qlString = "select m from Member m where m = :member";
List resultList = em.createQuery(qlString).setParameter("member", member).getResultList();
```

식별자를 사용하는 코드

```
String qlString = "select m from Member m where m.id = :memberId";
List resultList = em.createQuery(sqlString).setParameter("memberId", 4L).getResultList();
```

이 둘의 결과는 같게 작동한다는 거..!\
\
\


#### 외래 키 값

이번엔 외래 키를 사용하는 방법을 봐보자\
일단 그냥 단순하게 엔티티를 사용하는 방법과 같이 보자\


```
Team team = em.find(Team.class, 1L);

String qlString = "select m from Member m where m.team = :team";
List resultList = em.createQuery(q1String).setParameter("team", team).getResultList();
```

이번엔 외래 키를 사용하는 방법\


```
String q1String = "select m from Member m where m.team.id = :teamId;"
List resultList = em.createQuery(q1String).setParameter("teamId", 1L).getResultList();
```

m.team.id라는 의미는 team과 member가 조인이 일어나있다는 것을 의미하지만 사실 이미 member 테이블서 team\_id를 **외래키**\
로 가지고 있기 떄문에 실제로 조인이 일어나지는 않는다. 물론 m.team.name막 이렇게 가지고 있지 않은 값을 조회한다면 조인은 일어난다\
그래서 결론적으로 m.team.id를 사용해도 조인은 일어나지 않는다는점\


\
\


## Named 쿼리

JPQL 쿼리는 크게 동적 쿼리, 정적 쿼리로 나눌 수 있다

* 동적 쿼리 : em.createQuery처럼 jpql을 문자열로 만들어서 전송하는 것을 의미
* 정적 쿼리 : 미리 정의한 쿼리에 이름을 부여해서 필요할 떄 사용할 수 있는 것이고 한 번 정의하면 다시 변경 불가능

\
Named 쿼리는 애플리케이션 로딩 시점에 jpql을 체크하고 미리 파싱해두기 때문에 오류를 빠르게 체크할 수 있으며\
미리 파싱해둔 것을 사용하기 때문에 성능도 빠르며 정적 sql이 생성됨으로 데이터베이스 조회 성능 최적화도 잘 된다\
\
그래서 사용하는 방법이 뭐냐 -> 애노테이션으로 정의\


코드로 보자\


```
@Entity
@NamedQuery(
    name = "Member.findByUsername",
    query = "select m from Member m where m.username = :username")
)
public class Member{
    ...
}
```

이렇게 선언해두고 실제 사용은\


```
List<Member> resultList = em.createNamedQeury("Member.findByUsername", Member.class)
                                .setParameter("username", "회원1")
                                .getResultList();
```

만약의 두 개 이상의 namedquery를 사용하고 싶다면 @NamedQueries({@NamedQuery, ... }) 이렇게 내부에 집어넣고 사용하면 가능\
\
\


\
\
\
\
\
\
\
\
\
\
