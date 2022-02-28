# 즉시 로딩과 지연 로딩

## 즉시 로딩과 지연 로딩

프록시 객체는 주로 연관된 엔티티를 지연 로딩할 떄 사용한다.

하지만 연관된 객체에 대한 처리가 고민될 수 있는데,

하나의 객체를 조회할때 연관된 객체도 함께 조회해 둘 것인지

하나의 객체를 조회하고 연관된 객체를 사용하는 시점에 조회할 것인지

이 2가지의 경우를 우리가 선택할 수 있다.

즉시 로딩(EAGER) : 엔티티를 조회할 때 연관된 엔티티도 즉시 조회. 하이버네이트는 가능하면 SQL 조인을 사용해서 한 번에 조회

지연 로딩(LAZY) : 연관된 엔티티를 프록시로 조회하고, 그 프록시를 실제로 사용할 때 초기화하면서 실제 데이터베이스에서 조회





### 즉시 로딩

@ManyToOne(fetch = FetchType.EAGER) 이렇게 속성값에 추가해줌으로 사용할 수 있다.

실제 실행하게 되면 연관되어 있는 테이블을 각각 순차적으로 조회하는 것이 아니라

즉시 로딩을 최적화 하기 위해서 가능하면 조인 쿼리를 사용해서 조회한다.

여기서 사용되는 조인은 기본적으로 외부조인(Outer join)을 사용하는데, 아니 내부조인(inner join)이 성능도 더 좋지 않나? 싶지만

기본적으로 @JoinColumn을 매길 때 nullable이 true이기 때문에 외부 조인이 사용되는 것이다

만약 즉시 로딩 시, 내부조인을 사용하고 싶다면

@JoinColumn(name = “TEAM\_ID”, nullable = false) 이렇게 nullable을 허용하지 않거나

@ManyToOne(fetch = FetchType.EAGER, optional = false) 이렇게 optional을 넣어줘야함





### 지연 로딩

@ManyToOne(fetch = FetchType.LAZY)

이건 결국은 연관 객체를 조회할 때 그때 영속성 컨테스트, db에 조회하는 것이기 때문에 프록시 객체를 리턴해주고 실제 사용될 때 까지 데이터의 로딩을 미룬다.

근데 만약에 영속성 컨텍스트에 이미 객체가 존재하고 있다면 굳이 프록시 객체를 사용할 필요가 없다는점..!

각 두 가지의 옵션은 각각의 장단점이 존재하고 있기 때문에 상황에 따라서 사용하면 좋다..!





### 활용

Member : Team = N : 1

Member : Order = 1 : N

Order : Product = N : 1

가정해보자

* Member, Team은 자주 함께 사용해서 즉시 로딩
* Member, Order는 가끔 사용해서 지연 로딩
* Order, Product는 자주 함께 사용되서 즉시 로딩

```java
@Entity
    public class Member {
        @Id
        private String id;

        private String username;
        private Integer age;

        @ManyToOne(fetch = FetchType.EAGER)
        private Team team;

        @OneToMany(mappedBy = "member", fetch = FetchType.LAZY)
        private List<Order> orderList;
    }
```

###

###

### fetch의 기본 전략

JPA에서 제공해주고 있는 **기본적인 fetch에 대한 설정값**은

@ManyToOne, @OneToOne : 즉시 로딩(뒤가 One = 현재 사용하는 위치에서 단일)

@OneToMany, @ManyToMany : 지연 로딩(뒤가 Many = 현재 사용하는 위치에서 Collection)

기본적으로 **연관된 엔티티가 하나이면 즉시 로딩을** 사용하고 컬렉션을 즉시 로딩하기에는 위험부담이 크기 때문에 **컬렉션은 지연 로딩을 사용**하고 있다.

하지만 그래도 역시 추천해주는 방법은 **모든 연관관계에 대해서 지연로딩**을 사용하는 것이 좋다. 개발은 이렇게 진행하고 나중에 개발이 완료단계에 왔을 때 필요한 곳에만 즉시로딩을 매겨주는 것도 좋은 방법이다.

**컬렉션에서 즉시로딩 사용시 주의**

컬렉션을 하나 이상 즉시 로딩하지 말자..

컬렉션은 항상 외부조인을 사용하자...

@ManyToOne, @OneToOne

* optional = false : 내부조인
* optional = true : 외부조인

@OneToMany, @ManyToMany

* optional = false : 외부조인
* optional = true : 외부조인





