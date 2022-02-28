# 실습 예제

## 요구사항 분석

회원 기능

* 회원 등록
* 회원 조회

상품 기능

* 상품 등록
* 상품 수정
* 상품 조회

주문 기능

* 상품 주문
* 주문 내역 조회
* 주문 취소

## 도메인 모델 분석

요구사항에서 나오는 엔티티는 총 4개

1. 회원
2. 상품
3. 주문
4. 주문상품

회원 - 주문은 1 : N (주문에는 회원의 id값을 외래키로 가지고 있다)

주문 - 상품은 N : N (주문에는 상품의 id값을 외래키로 가지고 있다)

회원 - Member

```java
package EntityMappingExample;

import lombok.Data;

import javax.persistence.*;

@Data
@Entity
public class Member {

    @Id
    @GeneratedValue
    @Column(name = "MEMBER_ID")
    private Long id;

    private String name;
    private String city;
    private String street;
    private String zipCode;
}
```

주문 - Order

```java
package EntityMappingExample;

import lombok.Data;

import javax.persistence.*;
import java.util.Date;

@Data
@Entity
@Table(name = "ORDERS")
public class Order {

    @Id
    @GeneratedValue
    @Column(name = "ORDER_ID")
    private Long id;

    @Column(name = "MEMBER_ID")
    private Long memberId;

    @Temporal(TemporalType.TIMESTAMP)
    private Date orderDate;

    @Enumerated(EnumType.STRING)
    private OrderStatus orderStatus;

    enum OrderStatus{
        ORDER, CANCEL
    }
}
```

주문 상품 - OrderItem

```java
package EntityMappingExample;

import lombok.Data;

import javax.persistence.*;

@Data
@Entity
@Table(name = "ORDER_ITEM")
public class OrderItem {
    @Id
    @GeneratedValue
    @Column(name = "ORDER_ITEM_ID")
    private Long id;

    @Column(name = "ITEM_ID")
    private Long itemId;

    @Column(name = "ORDER_ID")
    private Long orderId;

    private int orderPrice;
    private int count;
}
```

상품 - Item

```java
package EntityMappingExample;

import lombok.Data;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Data
@Entity
public class Item {
    @Id
    @GeneratedValue
    @Column(name = "ITEM_ID")
    private Long id;

    private String name;
    private int price;
    private int stockQuantity;
}
```

지금 근데 연관관계에 있어서 연관 관계를 객체 지향적으로 풀어내지 못하고 있다... 일단은 그냥 요구사항을 통해서 ERD, UML을 만들어내고, 이렇게 엔티티를 구성하고 만들 수 있다는 점을 알 수 있다.
