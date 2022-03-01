# 객체지향 쿼리 언어

JPA는 복잡한 검색 조건을 사용해서 엔티티를 조회할 수 있는 기능을 제공해주고 있다.





## 객체 지향 쿼리 소개

EntityManager.find() 메소드를 사용하면 엔티티 하나를 조회할 수 있다.

조죄한 엔티티에 객체 그래프 탐색을 통해서 연관된 엔티티를 찾는다.

단순하게 EntityManager.find() - 식별자로 조회, .getB(), getC() - 객체 그래프 탐색을 통해서 검색해볼 수 있다.

유용한 특징으로는

* 테이블이 아닌 객체를 대상으로 검색하는 객체지향 쿼리
* SQL을 추상화해서 특정 데이터베이스 SQL에 의존하지 않는다

SQL이 데이터베이스의 테이블을 대상으로 하는 데이터 중심의 쿼리라면 JPQL은 엔티티 객체를 대상으로 하는 객체지향 쿼리이다.

엔티티 객체를 대상이기 때문에 객체지향 SQL이기 때문에 기존의 SQL과 비슷하다!

JPA에서 제공해주는 조회 기능은

**JPQL(Java Persistence Query Language)**

**Criteria 쿼리 : JPQL을 편하게 작성하도록 도와주는 API, 빌더 클래스 모음**

**네이티브 SQL : JPA에서 JPQL대신 직접 SQL을 사용할 수 있게 해줌**

**QueryDSL : Criteria 쿼리처럼 JPQL을 편하게 작성하도록 도와주는 빌더 클래스 모음**

**JDBC 직접 사용, MyBatis같은 SQL 매퍼 프레임워크 사용**

****

****

## JPQL?

Java Persistence Query Language는 엔티티 객체를 조회하는 객체지향 쿼리이다.

JPQL은 SQL을 추상화해서 특정 데이터베이스에 의존하지 않는다.

그리고 데이터베이스 방언만 살짝만 처리해주면 어렵지 않게 다른 데이터베이스로 변경이 가능

JPQL은 SQL보다 간단하다

```java
String jpql = "select m from Member as m where m.username = 'kim'";
List<Member> resultList = em.createQuery(jpql, Member.class).getResultList();
```

단순하게 String으로 쿼리를 작성하고

createQuery 함수로 실행할 쿼리 + 반환할 엔티티 타입을 넘겨주고

getResultList()로 결과를 뽑아주면 어렵지 않게 잘 나온다.

굳이 별칭을 사용해서 쿼리를 작성한건, 하이버네이트 구현체가 생성한 SQL은 별칭이 많이 복잡해서...





## Criteria 쿼리?

이건 JPQL을 생성하는 빌더 클래스이다.

단순한 문자열을 사용해서 짜는 것이 아니라 builder 코드를 통해서 메소드체인으로

**프로그래밍 코드로 JPQL을 작성하는 것이 가능함**

근데 왜 프로그래밍 코드로 만들어주냐?

* 컴파일 시점에 오류를 발견할 수 있다.
* IDE를 사용하면 코드 자동완성을 지원한다.
* 동적 쿼리를 작성하기 편하다

나중에 문자열로 쿼리를 만들어서 완벽한 검증없이 운영에 올라가면 당연히 문제이기 때문에 이런 방식을 사용

위의 문자열로 작성한 쿼리를 Criteria로 사용하면

```java
CriteriaBuilder cb = em.getCriteriaBuilder();
CriteriaQuery<Member> query = cb.createQuery(Member.class);

Root<Member> m = query.from(Member.class);

CriteriaQuery<Member> cq = query.select(m).where(cb.equal(m.get("username"), "kim"));
List<Member> resultList = em.createQuery(cq).getResultList();
```

m.get은 현재 컬럼명을 문자열로 가져와서 조회하고 있었는데

이런건 MetaModel API을 사용해서 코드로 수정할 수 있다

```java
m.get("username") -> m.get(Member_.username)
```

근데 나중에 쿼리가 길어지고 하면 분석하기 너무 힘들다는점..!





## QueryDSL?

이것도 위와 Criteria와 같은 JPQL 빌더역할이다. 하지만 Criteria보다 훨-씬 쉽다고하네

```java
JPAQuery query = new JPAQuery(em);
QMember member = QMember.member;

List<Member> members = query.from(member)
												.where(member.username.eq("kim"))
												.list(member);
```

음 보면 위보다는 더욱 알아보긴 쉽네...

QueryDSL은 애노테이션 프로세서를 사용해서 쿼리 전용 클래스를 만들어야함





## 네이티브 SQL?

JPQL을 사용하면서도 특정 데이터베이스에만 존재하는 기능을 사용해야 하는 경우가 존재한다.

그러한 경우에는 표준화되지 않았기 때문에 JPQL을 사용할 수 없고 그러한 경우에 사용하는 것이 네이티브 SQL

역시 단점은 데이터베이스가 변경될 때마다 네이티브 SQL도 수정해야함

```java
String sql = "SELECT ID, AGE, TEAM_ID, NAME FROM MEMBER WHERE NAME ='kim'";
List<Member> resultList = em.createNativeQuery(sql, Member.class).getResultList();
```





## JDBC 직접사용, MyBatis같은 SQL 매퍼 프레임워크 사용

직접적으로 JDBC 커넥션에 직접 접근하고 싶다면 JPA에서 제공하는 방법을 사용해야한다.

```java
Session session = entityManager.unwrap(Session.class);
session.doWork(new Work(){
		@Override
		public void execute(Connection connection) throws SQLException{
				//구현..
		}
});
```

먼저 JPA EntityManager에서 하이버네이트 session을 구현하고 doWork()으로 사용

JDBC나 MyBatis를 JPA와 함께 사용하면 영속성 컨텍스트를 적절한 시점에 강제로 flush해줘야한다.

이러한 방식은 JPA를 우회해서 직접적으로 데이터베이스에 접근하는 방식이기 때문에

JPA가 인식하지 못하고 영속성 컨텍스트와 데이터베이스의 데이터 불일치를 처리해줄 수 없다는 점이다

해결하는 방법은 SQL을 실행하기 직전에 영속성 컨텍스트를 수동으로 flush해서 데이터베이스와 영속성 컨텍스트를 동기화 해둬야 한다.

참고로 스프링은 JPA와 MyBatis를 쉽게 통합해서 사용할 수 있다.













