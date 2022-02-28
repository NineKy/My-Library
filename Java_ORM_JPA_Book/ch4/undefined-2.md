# 실습 예제

## 다양한 연관관계 매핑 예제

주문과 배송

Orders, Delivery은 1:1 관계, order에서 delivery에 접근하기 위해서는 외래 키를 order에 둔다.

일대일 관계이기 때문에 orders 테이블에 있는 deliveryID는 유니크

상품과 카테고리

하나의 상품은 여러가지의 카테고리에 들어갈 수 있고 하나의 카테고리에서 여러가지의 상품이 들어가기 때문에 order : category = n : n

Category, Item은 양방향으로 다대다 관계라고 볼 수 있다.

@ManyToMany, @JoinTable - name, @JoinColumn, @InverseJoinColumn 을 사용해서 CategoryItem이라는 매핑 테이블을 통해서 매핑했다.

이 방법 이외에도 따로 테이블을 만들어서도 해보면 좋을 것 같다..! - 내일? 오늘? 해보자

Member

```java
package com.example.jpa_project.ch06HW;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Entity
public class Member {
    @Id
    @GeneratedValue
    @Column(name = "MEMEBR_ID")
    private Long Id;

    private String name;
    private String city;
    private String street;
    private String zipCode;

}
```

Orders

```java
package com.example.jpa_project.ch06HW;

import javax.persistence.*;
import java.util.Date;

@Entity
public class Order {
    @Id
    @GeneratedValue
    @Column(name = "ORDER_ID")
    private Long id;

    private Date orderDate;

    @OneToOne
    @JoinColumn(name = "DELIVERY_ID")
    private Delivery delivery;

    @ManyToOne
    @JoinColumn(name = "MEMBER_ID")
    private Member member;

    @Enumerated(EnumType.STRING)
    private DeliveryStatus deliveryStatus;
}
```

OrderStatus

```java
package com.example.jpa_project.ch06HW;

public enum DeliveryStatus {
    READY, DONE
}
```

Delivery

```java
package com.example.jpa_project.ch06HW;

import javax.persistence.*;

@Entity
public class Delivery {
    @Id
    @GeneratedValue
    @Column(name = "DELIVERY_ID")
    private Long id;

    private String city;
    private String street;
    private String zipCode;

    @Enumerated(EnumType.STRING)
    private DeliveryStatus deliveryStatus;

    @OneToOne(mappedBy = "ORDER_ID")
    private Order order;
}
```

Category

```java
package com.example.jpa_project.ch06HW;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Entity
public class Category {
    @Id
    @GeneratedValue
    @Column(name = "CATEGORY_ID")
    private Long id;

    private String name;

    @ManyToOne
    @JoinColumn(name = "PARENT_ID")
    private Category parent;

    @OneToMany(mappedBy = "parent")
    private List<Category> categoryList = new ArrayList<>();

    @ManyToMany
    @JoinTable(
            name = "CATEGORY_ITEM",
            joinColumns = @JoinColumn(name = "CATEGORY_ID"),
            inverseJoinColumns = @JoinColumn(name = "ITEM_ID")
    )
    private List<Item> items = new ArrayList<>();
}
```

Item

```java
package com.example.jpa_project.ch06HW;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Entity
public class Item {
    @Id
    @GeneratedValue
    @Column(name = "ITEM_ID")
    private Long id;

    private String name;
    private int amount;
    private int stockQuantity;

    @ManyToMany(mappedBy = "items")
    private List<Category> categories = new ArrayList<>();

}
```















