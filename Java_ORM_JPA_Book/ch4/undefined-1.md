# 일대일, 다대다 관계

## 일대일, 다대다 관계



### 일대일

1:1의 관계는 양쪽이 서로 하나의 관계만을 가지게 된다. 특징으로는

* 일대일 관계는 그 반대도 일대일 관계이다
* 테이블 관계에서 일대다, 다대일은 항상 다쪽에 외래키를 갖게 되지만, 일대일은 양 테이블 중 어디에든 외래키가 들어가던 상관 없다

외래키가 어디에 들어가든 상관 없기 때문에 어느 특정한 테이블을 정해줘야 들어갈 수 있게 되는데, 어떻게 선택할 것인가? 그러면 관계의 주가 되는 테이블에 있는 경우와 그 반대의 경우가 있겠다

주 객체에 외래키가 있을 때는 기존에 객체 지향에서 사용하는 것 처럼 외래키를 사용해서 다른 객체를 참조해서 사용하고 있기 때문에 익숙하다고 볼 수 있다. 이것의 장점으로는 주 테이블만 확인해도 어떤 테이블과 관계를 맺고 있는지 확인할 수 있다는 점이 장점이다.

그럼 반대로 주가 아닌 객체에 외래키가 있는 경우는 옛날의 데이터베이스 방식이라고 볼 수 있다. 그렇다고 절 대 나쁜 방법은 아니라고 한다. 이 방식을 사용한 경우에는 어렵지 않게 일대일 방식에서 일대다 방식으로 변경할 수 있다는 것이 장점이다.

이번 예시는 기존의 Member와 각 member에게 할당되어 있는 하나의 Locker 테이블로 진행된다.



#### 주 객체에 외래키가 있는 경우의 단방향

Member가 주 객체가 되기 때문에 Member에서 Locker에 대한 객체를 가지고 있어줘야 한다.

Memeber

```java
package com.example.jpa_project.ch06Example;

import lombok.Data;

import javax.persistence.*;

@Entity
@Data
public class Member {
    @Id
    @GeneratedValue
    @Column(name = "MEMBER_ID")
    private Long id;

    private String username;
    
    @OneToOne
    @JoinColumn(name = "LOCKER_ID")
    private Locker locker;

    @ManyToOne
    @JoinColumn(name = "TEAM_ID", insertable = false, updatable = false)
    private Team team;
}
```

Locker

```java
package com.example.jpa_project.ch06Example;

import lombok.Data;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Entity
@Data
public class Locker {
    @Id
    @GeneratedValue
    @Column(name = "LOCKER_ID")
    private Long id;
    
    private String name;
}
```

이렇게 Member에서 @OneToOne으로 일대일인 것을 보여주고, 기존의 방식과 같게 @JoinColumn(name=키)이렇게 외래키에 대한 정의를 해주었다.



#### 주 객체에 외래키가 있는 경우 양방향

단방향에서는 주 객체에서만 @OneToOne을 통해서 표시를 해주었지만 양방향인 경우에는 주가 아닌 대상 객체에서도 처리를 해줘야겠지..?

Member - 이건 위와 같겠고

```java
package com.example.jpa_project.ch06Example;

import lombok.Data;

import javax.persistence.*;

@Entity
@Data
public class Member {
    @Id
    @GeneratedValue
    @Column(name = "MEMBER_ID")
    private Long id;

    private String username;
    
    @OneToOne
    @JoinColumn(name = "LOCKER_ID")
    private Locker locker;

    @ManyToOne
    @JoinColumn(name = "TEAM_ID", insertable = false, updatable = false)
    private Team team;
}
```

Locker

```java
package com.example.jpa_project.ch06Example;

import lombok.Data;

import javax.persistence.*;

@Entity
@Data
public class Locker {
    @Id
    @GeneratedValue
    @Column(name = "LOCKER_ID")
    private Long id;

    private String name;
    
    @OneToOne(mappedBy = "MEMBER_ID")
    private Member member;
}
```

이제는 반대쪽에서도 해당 정보를 가지고 있어야 하기 때문에 @OneToOne을 통해서 Member의 ID로 매핑되었다는 정보를 적어주어야 한다.





### 다대다

기본적으로 관계형 데이터 베이스에서는 테이블 2개를 가지고 다대다 관계를 표현할 수 없다. 보통은 일대다, 다대일 관계로 풀어내서 다대다 관계를 표현하곤 하는데, 이러한 방법은 2개의 테이블 사이에 연결 테이블을 하나 더 추가하는 방식을 통해서 구현해 낸다.

하지만..! 객체 관계에서는 다대다의 관계를 표현할 수 있다는 점이 있다. 2개의 객체 서로 컬렉션을 사용해서 서로의 객체를 참조하면 어렵지 않게 구현할 수 있다는 점이 있다.

이번의 예시로는 Member와 Product로 구현



#### 다대다 단방향

Product

```java
package com.example.jpa_project.ch06Example;

import lombok.Data;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Entity
@Data
public class Product {
    @Id
    @GeneratedValue
    private String Id;

    private String name;
}
```

Member

```java
package com.example.jpa_project.ch06Example;

import lombok.Data;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Entity
@Data
public class Member {
    @Id
    @GeneratedValue
    @Column(name = "MEMBER_ID")
    private Long id;

    private String username;

    @OneToOne
    @JoinColumn(name = "LOCKER_ID")
    private Locker locker;

    @ManyToOne
    @JoinColumn(name = "TEAM_ID", insertable = false, updatable = false)
    private Team team;

    @ManyToMany
    @JoinTable(
            name = "MEMBER_PRODUCT",
            joinColumns = @JoinColumn(name = "MEMBER_ID"),
            inverseJoinColumns = @JoinColumn(name = "PRODUCT_ID")
    )
    private List<Product> products = new ArrayList<>();
}
```

음.. 뭔가 많아 졌네...

Memeber 엔티티와 Product 엔티티를 매핑했는데, 방식으로는 @ManyToMany와 @JoinTable을 사용해서 진행했다. 이런 방식을 사용하게 되면 따로 Member\_Product엔티티가 없어도 매핑을 할 수 있다는 점!

@JoinTable의 속성

* name : 연결 테이블을 지정한다.
  * 위의 같은 경우에는 Member측에서는 MemberId를, Product측에서는 ProductId를 내새웠기 때문에 Member\_Product 엔티티의 구성은 위의 2 키를 PK, FK로 들고 있는 상황이 된다.
* joinColumns : 현재 방향인 Member와 매핑할 조인 컬럼을 적어줌
* inverseColumns : 반대 방향인 Product와 매핑할 조인 컬럼을 적어줌

신기하게도 이렇게 엔티티를 구성하고 저장하는 과정에서 product와 member를 세팅하고 persist를 통해 저장을 해준다면 신기하게도 연결 테이블에도 값이 저장된다.

조회할때도 자동으로 Member\_Product테이블을 통해서 조회를 해준다..! 완전편리



#### 다대다 양방향

기존에 일대일 단방향에서 양방향으로 바뀔때랑 같이 Product에도 Member에 대한(주인)에 대한 정보를 입력해주면 된다 → **mappedBy가 없는 곳이 주인이다!!**

Member

```java
package com.example.jpa_project.ch06Example;

import lombok.Data;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Entity
@Data
public class Member {
    @Id
    @GeneratedValue
    @Column(name = "MEMBER_ID")
    private Long id;

    private String username;

    @OneToOne
    @JoinColumn(name = "LOCKER_ID")
    private Locker locker;

    @ManyToOne
    @JoinColumn(name = "TEAM_ID", insertable = false, updatable = false)
    private Team team;

    @ManyToMany
    @JoinTable(
            name = "MEMBER_PRODUCT",
            joinColumns = @JoinColumn(name = "MEMBER_ID"),
            inverseJoinColumns = @JoinColumn(name = "PRODUCT_ID")
    )
    private List<Product> products = new ArrayList<>();
}
```

Product

```java
package com.example.jpa_project.ch06Example;

import lombok.Data;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.persistence.ManyToMany;
import java.util.ArrayList;
import java.util.List;

@Entity
@Data
public class Product {
    @Id
    @GeneratedValue
    private String Id;

    private String name;
    
    @ManyToMany(mappedBy = "products")
    private List<Member> members = new ArrayList<>();
}
```

기본적인 ManyToMany 관계는 이러하다.



#### 다대다의 한계점, 해결방안

이렇게만 되면 정말 행복하겠지만

한계점이 있다... 위와 같은 예시에서 Member와 Product의 매핑 사이에 날짜와 같은 새로운 컬럼이 추가되야 하는 경우가 있는데, 새로운 컬럼이 추가되게 되면 @ManyToMany로는 다대다 관계를 표현할 수 없고, 연결 엔티티를 따로 만들고 그 컬럼들에 기존의 매핑해야할 두 컬럼을 매핑해서 일대다, 다대일 관계로 풀어내야 한다..!

이번에는 그 연결 엔티티를 MemberProduct로 풀어내고자 한다.

그럼 일단 기존의 ManyToMany를 OneToMany, ManyToOne으로 해체를 시켜줘야한다.

일단 Member와 MemberProduct는 양방향 관계로 만들었고, 외래 키는 MemberProduct에서 가지고 있기 때문에 주 객체는 MemberProduct가 되고 Member에는 mappedBy로 주가 아님을 표현

Member

```java
package com.example.jpa_project.ch06Example;

import lombok.Data;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Entity
@Data
public class Member {
    @Id
    @GeneratedValue
    @Column(name = "MEMBER_ID")
    private Long id;

    private String username;
    
    @OneToMany(mappedBy = "member")
    private List<MemberProduct> memberProducts;
}
```

Product

여기에는 연관관계를 만들지 않았다.

MemberProduct

```java
package com.example.jpa_project.ch06Example;

import javax.persistence.*;
import java.time.LocalDate;

@Entity
@IdClass(MemberProductId.class)
public class MemberProduct {
    @Id
    @ManyToOne
    @JoinColumn(name = "MEMBER_ID")
    private Member member;

    @Id
    @ManyToOne
    @JoinColumn(name = "PRODUCT_ID")
    private Product product;

    private LocalDate createdAt;
}
```

MemberProductId

```java
package com.example.jpa_project.ch06Example;

import java.io.Serializable;
import java.util.Objects;

public class MemberProductId implements Serializable {
    //각 MemberProduct의 member, product객체에 연결
    private String member;
    private String product;

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        MemberProductId that = (MemberProductId) o;
        return Objects.equals(member, that.member) && Objects.equals(product, that.product);
    }

    @Override
    public int hashCode() {
        return Objects.hash(member, product);
    }
}package com.example.jpa_project.ch06Example;

import javax.persistence.*;
import java.time.LocalDate;

@Entity
@IdClass(MemberProductId.class)
public class MemberProduct {
    @Id
    @ManyToOne
    @JoinColumn(name = "MEMBER_ID")
    private Member member;

    @Id
    @ManyToOne
    @JoinColumn(name = "PRODUCT_ID")
    private Product product;

    private LocalDate createdAt;
}package com.example.jpa_project.ch06Example;

import javax.persistence.*;
import java.time.LocalDate;

@Entity
@IdClass(MemberProductId.class)
public class MemberProduct {
    @Id
    @ManyToOne
    @JoinColumn(name = "MEMBER_ID")
    private Member member;

    @Id
    @ManyToOne
    @JoinColumn(name = "PRODUCT_ID")
    private Product product;

    private LocalDate createdAt;
}
```

MemberProduct에서 다 같은데 @IdClass가 눈에 띈다. 이 것을 사용해서 복합 기본 키를 매핑한다.

**복합 기본 키**

MemberProduct의 기본 키는 MemberId + ProductId로 구성되어 있고 이런 것을 보고 복합 기본 키라고 한다.

JPA에서 복합 기본 키를 사용하기 위해서는 **별도의 식별자 클래스**를 만들어야 하며, 그 별도의 식별자 클래스를 @IdClass를 통해서 지정해줘야한다는 특이점이 있다.

식별자 클래스의 특징

* 복합 기본 키는 별도의 식별자 클래스를 만들어야 한다.
* Serializable을 구현해야 한다.
* equals와 hashCode의 메소드를 구현해야 한다(그냥 ide에서 제공해주는 걸로 추가하자)
* 기본 생성자가 있어야한다.
* 식별자 클래스의 접근 타입은 public
* @IdClass말고 @EmbeddedId를 사용하는 방법도 있음

식별 관계

MemberProduct의 기본 키는 Member, Product의 기본 키를 받아서 자신의 기본 키로 사용하는데, 이렇게 부모 테이블의 기본 키를 받아서 자신의 기본키, 외래키로 사용하는 관계를 보고 **식별 관계** 라고 부른다.

이렇게 귀찮은 복합 키를 사용하지 않고도 간단하게 사용하는 방법도 있다는 점!



#### 다대다 새로운 기본 키 사용

가장 추천하는 키 생성 전략은 그냥 DB에서 자동으로 생성해주는 대리 키를 Long값으로 사용하는 것이다..!

여기서 가장 큰 장점은 반 영구적으로 사용이 가능하며, ORM 매핑 시에 복합 키를 만들지 않아도 된다는 점!!

그럼 위에서 만들었던 MemberProduct 엔티티의 이름을 Orders로 변경해보자

Orders

```java
package com.example.jpa_project.ch06Example;

import lombok.Data;

import javax.persistence.*;
import java.time.LocalDate;

@Entity
@Data
public class Orders {
    @Id
    @GeneratedValue
    @Column(name = "ORDER_ID")
    private Long id;
    
    @ManyToOne
    @JoinColumn(name = "MEMBER_ID")
    private Member member;
    
    @ManyToOne
    @JoinColumn(name = "PRODUCT_ID")
    private Product product;
    
    private LocalDate createdAt;
}
```

이렇게 굳이 키를 받아서 조합해서 사용하는 것 보다는 그냥 새로운 기본 키를 만들어서 사용하는 것이 몇 배는 더 간결해진 것 같다.



#### 다대다 → 일대다 + 다대일 : 식별자의 구성

식별 관계 : 부모 테이블에서 받아온 식별자를 기본 키 + 외래 키로 사용

비식별 관계 : 받아온 식별자를 외래 키로만 사용하고 새로운 식별자를 추가

객체 지향적인 관점에서는 비식별 관계가 더욱 편리하게 ORM매핑을 사용할 수 있다는 점!









