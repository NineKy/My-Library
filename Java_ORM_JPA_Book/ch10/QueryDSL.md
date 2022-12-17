# QueryDSL

QueryDSL은 오픈소스 프로젝트이고 다수의 프로젝트에서 지원되고 있는 상황이다\
_이게 뭔가 -> jpql을 사용해서 쿼리 즉 데이터를 조회하는데 기능이 특화되어 있는 프로젝트_\
\
\


## 사용 방법

기본적으로 제공해주는 기능은 아니기 때문에 따로 라이브러리를 주입받아야 함\
querydsl-jpa : QueryDSL JPA 라이브러리\
querydsl-apt : 쿼리 타입을 생성할 때 필요한 라이브러리\
여기서 나온 2가지 라이브러리는 필수..! 필요할 때 구글링해서 설치해 사용하자\
\


QueryDSL을 사용하기 위해서는 우선적으로 JPAQuery 객체를 만들어야 한다\
이때 엔티티 매니저를 생성자에게 넘겨준 뒤, 다음으로 사용할 쿼리 타입을 생성하는데\
생성자에는 별칭을 주면 되고, 이 별칭은 jpql에서 사용하던 방식과 동일하게 진행하면 된다\


코드로 한번 봐보자\


```
public void queryDSL(){
    EntityManager em = emf.createEntityManager();
    
    JPAQuery query = new JPAQuery(em);
    QMember qMember = new QMember("m");
    List<Member> members = query
                             .from(qMember)
                             .where(qMember.name.eq("회원1"))
                             .orderBy(qMember.name.desc())
                             .list(qMember);
}
```

객체를 만들고뭐 세팅을 제외하고 실제로 쿼리를 만드는 부분을 보면 그렇게 어렵지 않게 sql문과 닮아있다..!\
\


쿼리 타입(Q) 여기서는 QMember로 만들었는데 요놈은 사용하기 편리하도록 기본 인스턴스를 보관하는게 좋긴 하다\
하지만 같은 엔티티를 조인하거나 같은 엔티티를 서브쿼리에 사용하면 같은 별칭이 사용되기 때문에 이럴때는 별칭을 직접 지정\
\
\


## 검색 조건 쿼리

where절에서 조건문을 작성해주는데 이 내부에서도 and 나 or와 같이 정말 쿼리문을 작성하듯 작성하는 것이 가능하다\


```
JPAQuery query = new JPAQuery(em);
QItem item = QItem.item;
List<Item> list = query.from(item)
                    .where(item.name.eq("좋은 상품").and(item.price.gt(20000)))
                    .list(item);
```

이렇게 .and, .or를 사용해서 조건을 넣을 수 있으며 추가적으로 where(item.name.eq("좋은상품"), item.price.gt(20000)) 이렇게 추가 파라미터를 사용해서하면 and가 됨\
\-> 이외에도 between, contains, startsWith등등 이렇게 다양하게 있으니까 intellij, 구글의 도움을 받으며 사용

\
\
\


## 결과 조회

쿼리의 작성이 끝나고 결과를 조회하는데 있어서 마지막에는 결과를 받을 메소드를 넣어줘야 한다\
대표적인 메소드들

* uniqueResult() : 조회 결과가 하나일 때 사용하고, 만약 없으면 null, 만약 결과가 여러개이면 Exception
* singleResult() : uniqueResult()와 동일하지만 만약 결과가 여러개이면 맨 앞에것 리턴
* list() : 결과가 하나 이상일 때 사용

\
\
\


## 페이징, 정렬

우선 정렬같은 경우는 orderBy메소드를 사용하는데 정렬 방식을 지정하는데는 asc(), desc()을 추가로 사용\
페이징같은 경우에는 offset메소드와 limit메소드를 사용해도 괜찮고 restrict() 메소드에 파라미터로 넣어줘도 가능하다- limit, offset 순서\
\
사용 예시를 보자\


```
QueryModifiers queryModifiers = new QueryModifiers(20L, 10L); 
List<Item> list = query.from(item)
                     .restrict(queryModifiers)
                     .list(item);
```

이외에도 정렬list를 뽑았을 때 getTotal, getLimit, getOffset, getResults와 같은 메소드를 통해서 결과들을 사용할 수 있다!\
\
\


## 그룹

우리가 알고 있는 groupBy, having을 통해서 사용할 수 있다\
\
\


## 조인

조인은 innerJoin, leftJoin, rightJoin, fullJoin 이렇게 존재하고 있으며 fetchJoin도 사용 가능\
조인의 기본 문법은 첫 번째 파라미터에서 조인 대상을 지정하고 두 번째 파라미터에서 별칭으로 사용할 쿼리 타입을 지정\
조인의 예시를 보자\


```
QOrder order = QOrder.order;
QMember member = QMember.member;
QOrderItem orderItem = QOrderItem.orderItem;

//기본적인 조인
query.from(order)
    .join(order.member, member)
    .leftJoin(order.orderItem, orderItem)
    .list(order);

//on을 사용한 조인
query.from(order)
    .leftJoin(order.orderItem, orderItem)
    .on(orderItem.count.gt(2))
    .list(order);
    
//패치 조인을 사용
query.from(order)
    .innerJoin(order.member, member).fetch()
    .leftJoin(order.orderItems, orerItem).fetch()
    .list(order);
    
//from 절에서 여러 조건 사용
query.from(order, member)
    .where(order.member.eq(member))
    .list(order);    
```

\
\
\


## 서브 쿼리

서브 쿼리는 사용하는데 있어서 JPASubQuery를 생성해서 사용하고,\
결과를 받는데 있어서는 기존과 비슷하게 unique(), list()를 사용\
서브 쿼리, 다수의 서브 쿼리를 사용하는 예시를 보자\


```
QItem item = QItem.item;
QItem itemSub = new QItem("itemSub");

//단일 서브 쿼리
query.from(item)
    .where(
        item.price.eq(
            new JPASubQuery().from(itemSub)
                .unique(itemSub.price.max())
        )
    )
    .list(item);
    
//여러 개의 서브 쿼리
query.from(item)
    .where(
        item.in(
            new JPASubQuery().from(itemSub)
                .where(item.name.eq(itemSub.name))
                .list(itemSub)
        )
    )
    .list()
```

\
\
\


## 프로젝션과 결과 반환

select 절에 조회 대상을 지정하는 것을 보고 프로젝션이라고 부름\
만약 결과를 다수의 컬럼으로 받고 싶다면 Tuple을 사용해서 진행할 수 있다\
만약 결과를 엔티티가 아닌 다른 특정 객체로 받고 싶다면 빈 생성을 통해서 결과를 받을 수 있는데 이러한 객체를 생성하는 방법은\
프로퍼티 접근, 필드 직접 접근, 생성자 사용 이렇게 나누어져 있다\
\
추가로 만약 distinct를 붙히고 싶다면\
query.distinct().from(테이블)... 이렇게 사용\
\
\
\


## 수정, 삭제 배치 쿼리

jpql 배치 쿼리와 같이 영속성 컨텍스트를 무시하고 데이터베이스에 직접적으로 쿼리한다는 점..!\
나중에 jpql 배치 쿼리를 다룬다..!\
\
\
\


## 동적 쿼리

동적 쿼리는 BooleanBuilder를 사용해서 특정 조건에 따른 동적 쿼리를 생성할 수 있다\
\
\


## 메소드 위임

메소드 위임을 사용하게되면 쿼리 타입에 검색 조건을 직접 정의하는 것이 가능하다\
검색 조건은 이렇게 정의한다\


```
public class ItemExpression{
    @QueryDelegate(Item.class)
    public static BooleanExpression isExpensive(QItem item, Integer price){
        return item.price.gt(price);
    }
}
```

이렇게 일단 정적메소드를 만들고, QueryDelegate으로 이 기능을 사용할 엔티티를 지정하고 거기에 애노테이션으로 정해준다\
정적 메소드의 첫 번째 파라미터는 대상 엔티티의 쿼리 타입을 지정하고 나머지는 필요한 파라미터를 지정\
\
\
\


## QueryDSL 정리

문자가 아닌 코드로 쿼리를 작성할 때, 복잡한 동적 쿼리를 어떻게 해결하냐에서 정말 좋은 답안을 주는 라이브러리이다!!\
그리고 결국은 컴파일러의 확인을 받고 쿼리를 실행하기도 하면서 쿼리를 작성하는데 있어서 ide의 도움도 받기 때문에\
안전하게 쿼리를 작성하는 것이 가능하기에 유용하다는 생각이 든다

\
\
\
\
\
\
\
\
\
\<br
