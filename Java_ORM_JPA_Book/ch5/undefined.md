# 상속 관계 매핑

## 상속 관계 매핑

관계형 데이터베이스는 객체지향 언어에서 다루는 상속이라는 개념이 존재하지 않음

그러면 상속이라는 개념 대신에 어떤 개념이 존재하고 있냐 → Super-Type Sub-Type Relationship 이라는 모델이 존재한다.

그럼 상속을 구현 = 슈퍼타입 서브타입 관계를 실제로 테이블로 어떻게 만들어 낼 것인가..?

3가지 방법이 존재한다

1. 각각의 테이블로 변환 - 각각 테이블로 만들고 조인 관계를 통해서 조회(=조인 전략)
2. 통합 테이블로 변환 - 테이블 하나 가지고 통합(=단일 테이블 전략)
3. 서브타입 테이블로 변환 - 서브 타입에 맞는 테이블만 만듬(=구현 클래스마다 테이블 전략)





#### 조인 전략(각각의 테이블로 변환)

각각의 테이블이라는 의미는 서브 테이블부터 슈퍼 테이블까지(부모부터 자식까지) 모두 테이블로 만들고 자식이 부모의 기본 키를 할당 받아서 기본 키 + 외래 키로 사용하는 전략이다.

그렇기 때문에 조회 시, 항상 조인이 일어나게 된다.

주의해야 하는 점은 테이블에서는 객체와 같이 타입으로 구분할 수 없기 때문에 타입으로 구분하는 컬럼을 추가해줘야 하고 여기서는 DTYPE 컬럼을 구분 컬럼으로 사용한다.

예제 코드

뭔가 처음보는 애노테이션이 짠뜩있다... 흠

```java
package com.example.jpa_project.ch07Example;

import javax.persistence.*;
import java.time.LocalDate;

public class JoinStrategyMapping {

    @Entity
    @Inheritance(strategy = InheritanceType.JOINED)
    @DiscriminatorColumn(name = "DTYPE")
    public abstract class Item{
        @Id
        @GeneratedValue
        @Column(name = "ITEM_ID")
        private Long id;

        private String name;
        private int price;

        private LocalDate createdAt;
        private LocalDate updatedAt;
    }

    @Entity
    @DiscriminatorValue("A")
    public class Album extends Item{
        private String artist;

    }

    @Entity
    @DiscriminatorValue("M")
    public class Movie extends Item{
        private String director;
        private String actor;
    }

    @Entity
    @DiscriminatorValue("B")
    @PrimaryKeyJoinColumn(name = "BOOK_ID") //아이디를 재정의한다..?
    public class Book extends Item{
        private String author;
        private String isbn;
    }
}
```

@Inheritance(strategy = InheritanceType.JOINED) : 상속 매핑은 부모 클래스에 @Inheritance를 사용해야 한다. 그리고 매핑 전략을 지정해야 하는데 여기서는 조인 전략을 사용하므로 inheritanceType.JOINED를 사용했다... 다른 전략도 존재하긴 하는구나

@DiscriminatorColumn(name = “DTYPE”) : 부모 클래스에 구분 컬럼을 지정하는데, 이 컬럼으로 저장된 자식 테이블을 구분할 수 있다 - default가 DTYPE이라서 속성 안붙혀줘도 무관

@DisciminatorValue(”M”) : 엔티티를 저장할 때 구분 컬럼에 입력할 값을 지정한다. 만약 영화 엔티티를 저장하면 구분 컬럼인 DTYPE에 값 M이 저장됨

@PrimaryKeyJoinColumn : 이건 부모의 기본 키를 받아와서 키로 사용하지만 이름을 변경해서 집어넣고 싶을 때 여기 name에 값을 넣어주니까 아이디가 바뀌어서 저장됨

그래서 장점

* 테이블이 정규화됨
* 외래 키 참조 무결성 제약조건을 활용할 수 있음
* 저장 공간을 효율적으로 사용

그러면 단점

* 조회할 때 너무 많은 조인이 일어나서 성능에 영향
* 조회 쿼리가 복잡
* 데이터를 등록할 insert sql을 두 번 실행





#### 단일 테이블 전략 (하나로 통합)

뭐 말 그대로 하나의 테이블로 만들고 구분 컬럼(DTYPE)으로 어떤 자식 데이터가 저장됬는지 구분한다.

자 일단 자식 엔티티가 매핑한 컬럼은 모두 null을 허용해야 한다 예시를 보자

```java
package com.example.jpa_project.ch07Example;

import javax.persistence.*;

public class SingleTableStrategyMapping {
    @Entity
    @Inheritance(strategy = InheritanceType.SINGLE_TABLE)
    @DiscriminatorColumn
    public abstract class Item{
        @Id
        @GeneratedValue
        @Column(name = "ITEM_ID")
        private Long id;

        private String name;
        private Long price;
    }

    @Entity
    @DiscriminatorValue("A")
    public class Album extends Item{
        private String artist;
    }

    @Entity
    @DiscriminatorValue("M")
    public class Movie extends Item{
        private String director;
        private String actor;
    }
    
    @Entity
    @DiscriminatorValue("B")
    public class Book extends Item{
        private String isbn;
        private String author;
    }
}
```

예를 들어서 Book을 만들어서 저장했다고 가정하면 나머지 Album의 필드인 artist, Movie의 필드인 director, actor의 항목은 null값으로 해서 들어가게 된다.

그래서 장점은

* 조인이 필요 없어서 조회 성능이 빠름
* 조회 쿼리가 단순

그러면 단점은

* 자식 엔티티가 매핑한 컬럼은 모두 nullable이여야함
* 단일 테이블이니까 값이 많아지면 테이블이 커지고 그 의미는 오히려 성능이 낮아질 수 있다는 점

구분 컬럼(DiscriminatorColumn)를 꼭 설정해줘야하고 DiscriminatorValue를 사용하지 않으면 기본적으로 엔티티의 이름을 사용한다.





#### 구현 클래스마다 테이블 전략

음.. 이건 그냥 abstract 클래스 구현시 붙히는 @Inheritance(strategy = InheritanceType.TABLE\_PER\_CLASS)를 붙히는 것만 있지 나머지는 위와 특별하게 다른점이 없다.

그냥 책에서도 추천하지 않는다고 말하고 있다 -> 모두가 쓰지말라는데 그냥 존재하고 있다는 점만 생각하자









