# Spring Data JPA

JDBC → Hibernate → JPA → Spring Data JPA

우리는 Spring Data JPA을 사용하는데 이건 repository를 활용해서 사용

DAO\(Data Access Object\)

만약 다른 entity에 대해서 또 DAO함수들을 만들어야한다면 이건 중복이라서 매우 귀찮게 된다..

항상 결과적으로는 entity type과 primary key 이 2개만 바뀌게되는 부분이고 나머지는 다 비슷하다.

그러면 Spring에게 entity type과 primary key을 주고, DAO을 만들라고 시키자!!

⇒ 결과적으로 DAO을 따로 만들 필요가 없다!

#### Spring Data JPA

다양한 인터페이스를 제공해주는 방식

public interface ProductRepository extends CrudRepository&lt;Entity Type, Primary Key type&gt;{}

* primarykey를 주는게아니라 primarykey의 타입을 주는거다!!!!!!!!!!!!!!

이런 방식으로 사용하고 이걸 자동으로 사용할 수 있다.

* findAll\(\)
* findById\(\)
* save\(\)
* deleteById\(\)

JpaRepository&lt;T, ID&gt; ⇒ PagingAndSortingRepository&lt;T, ID&gt; ⇒ CrudRepository&lt;T, ID&gt;

\(가장 하단\)

PagingAndSortingRepository에서는

Pageable paging = PageRequest.of\(pageNo, pageSize, [Sort.by](http://sort.by/)\(""\)\);

이렇게 만들어서 사용하고 인자로는 페이지번호, 페이지에 몇개 들어갈 것인가 , 뭘로 정리할 것인지

Test과정에서 @Test라는 애노테이션을 클래스에 붙힌다.

클래스 안에서 assertNotNull\(\) 인자로 들어간것이 null이 되면 안된다, assertEquals\(비교1, 비교2\)은 맞는지 확인

#### Query Method

원래는 CrudRepository에서 제공해주는 함수들만 사용을 했었는데 , 다른 필요한 쿼리가 생길수도 있는 경우가 있다!!! 그래서 이런 부분들을 해결하기 위해서 우리만의 custom 쿼리문을 작성

method이름만 주면 dynamic query generation을 기반해서 만든다.

= 메소드만 만들어주면 메소드 이름을 보고 자동으로 sql문을 구현해준다.

대신 만드는 메소드는 find + By + 변수이름 방식으로 만들어야만 한다!!

And, Or, LessThan, GreaderThan, IsNull, Null, IsNotNull, NotNull, Like, NotLike, StartingWith, EndingWith, Containing 등의 키워드들을 사용하는 것이 가능하다

findByTitleContainingOrContentContaining\(String title, String content\);

= 제목에 17이 포함되어있거나 내용에 17이 포함되어있는 항목을 찾는다.

페이징단위로 조회하고 싶다면

Pageable paging = PageRequest\(0,5\); ⇒ 페이지번호\(0부터시작\), 페이지사이즈\(페이지에 몇개를 담을 것인지\)

findByTitleContaining\(String keyword, paging\); 요렇게 만들어주면 된다

#### @Query Annotation : JPQL

너무 복잡한 쿼리가 필요할경우에는

@Query\("쿼리문~"\)

함수

이런 방식으로 사용할 수 있다.

Positional parameter binding 방식

쿼리문을 작성할때 ?숫자위치을 집어넣어두고 밑에 함수에서 매개변수안에 ?에 들어갈 값을 넣어주면 binding되어서 실행된다

Name parameter binding 방식

쿼리문을 작성할때 :변수이름 이렇게 집어넣어두고 밑에 함수에서 매개변수 안에 @Param\("변수이름"\) 이렇게 값을 지정해주면 알아서 binding 되어서 실행된다

원래는 JPQL방식으로 작성을하지만 만약 native SQL을 사용하고 싶다면 native로 작성후에 ,nativeQuery=true을 넣어주면 된다

href="/new"

vs th:href ="/new"

context-path : helloSpring

href ⇒ localhost:8080/new

th:href ⇒ localhost:8080/helloSpring/new

schema.sql이라는 파일을 만들어서 수동으로 테이블을 만드는것도 가능하고, 아니면 굳이 schema.sql파일을 만들지 않고, application.properties에서

spring.jpa.generate-ddl = true , spring.jpa.hibernate.ddl-auto=create라는 설정값을 줘서 자동으로 값이 들어가도록하는 것도 가능하다

spring.jpa.hibernate.ddel-auto=create을 주게 되면 자동으로 테이블을 만들어준다!

