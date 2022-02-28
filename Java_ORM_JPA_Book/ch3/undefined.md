# 실습 예제



### 예제

책에 나와있는(p196) ERD, UML을 기반으로 엔티티를 만들어보았다.

배운점

1. 지금 작성하고 있는 엔티티에 다른 엔티티가 다수로 들어오면 OneToMany(mappedBy=””)에 객체 적어주자
2. 지금 작성하고 있는 엔티티를 다른 엔티티에서 사용하면 ManyToOne + ManyToOne사용할 때는 JoinColumn(name=””)에 외래키를 작성하자
3. **결과적으로 지금 작성하고 있는 엔티티가 일이냐 다수냐를 파악하자!**
4. @Id를 사용할 때 진짜 객체에서는 id로 사용하기 때문에 @Column을 사용해서 엔티티에 맞는 id를 작성해두자

Member

```java
package ch05;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Entity
public class Member {
    @Id
    @GeneratedValue
    @Column(name = "MEMBER_ID")
    private int id;

    private String name;

    private String city;

    private String street;

    private String zipCode;

    @OneToMany(mappedBy = "member")
    private List<Order> orders = new ArrayList<>();
}
```

Order

```java
package ch05;

import javax.persistence.*;
import java.time.LocalDate;
import java.util.LinkedList;
import java.util.List;

@Entity
public class Order {
    @Id
    @GeneratedValue
    @Column(name = "ORDER_ID")
    private Long id;

    @ManyToOne
    @JoinColumn(name = "MEMBER_ID")
    private Member member;

    @OneToMany(mappedBy = "order")
    private List<OrderItem> orderItemList = new LinkedList<>();

    private LocalDate orderDate;

    @Enumerated(EnumType.STRING)
    private OrderStatus status;

    enum OrderStatus{
        DONE, DELIVERY, READY
    }
}
```

OrderItem

```java
package ch05;

import javax.persistence.*;

@Entity
public class OrderItem {
    @Id
    @GeneratedValue
    @Column(name = "ORDER_ITEM_ID")
    private Long id;

    @ManyToOne
    @JoinColumn(name = "ORDER_ID")
    private Order order;

    @ManyToOne
    @JoinColumn(name = "ITEM_ID")
    private OrderItem orderItem;

    private Long orderPrice;
    private Long count;
}
```

Item

```java
package ch05;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Entity
public class Item {
    @Id
    @GeneratedValue
    @Column(name = "ITEM_ID")
    private Long id;

    private String name;
    private Long price;
    private String stockQuantity;
}
```







