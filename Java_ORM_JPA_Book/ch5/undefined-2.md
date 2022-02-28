# 조인 테이블

데이터 베이스에서 테이블의 연관관계를 설계하는 방법은 크게 2가지이다.

조인 컬럼의 사용 - 외래 키 사용

조인 테이블 사용 - 테이블 사용

조인 컬럼은 우리가 흔히 알고 있는 외래키를 조인하는 테이블에다가 줘서 외래 키를 통해서 조인 관계를 관리하는 방법이다. 그리고 연결 테이블, 링크 테이블이라고도 불리운다

살짝 이전 것의 이야기를 보면 설계는 조인 컬럼을 가지고 있는 것으로 설계가 되어도 실제로 데이터가 입력되기 전까지는 **빈 값**으로 처리되는 경우가 있다. 이렇게 외래 키에 null을 허용하는 관계를 보고 선택적 비식별 관계라고 한다. 그리고 외래 키에 null을 허용해야하기 때문에 외부 조인을 사용하게 된다.

하지만 조인 테이블은 뭐냐 → 외래 키를 이용하긴 별도의 테이블을 만들어서 그 외래 키를 관리함을 통해서 연관관계를 관리하는 방법이다.

이름 그대로의 역할이지만 테이블을 하나 새로 만드는 일이기 때문에 솔직히 관리해야 하는 테이블이 늘어난다는 것이 그렇게 좋은 일은 아니라고 생각한다. 또한 2개의 테이블을 조인하는데 있어서 2개의 테이블만 조인하는 것이 아니라 3개의 테이블까지 조인해야 하는 단점이 존재한다는 점이 있다.

그래서 기본적으로는 조인 컬럼을 사용하되, 필요한 경우에는 조인테이블을 사용해주자

####

####

#### 일대일 조인 테이블

일대일 관계를 만들기 위해서는 조인 테이블의 외래 키 컬럼에 각각 총 2개의 유니크 제약조건을 걸어야 한다.

```java
package com.example.jpa_project.ch07Example;

import javax.persistence.*;

public class JoinTable_OneToOne {
    @Entity
    public class Parent{
        @Id
        @GeneratedValue
        @Column(name = "PARENT_ID")
        private Long id;
        private String name;

        @OneToOne
        @JoinTable(name = "PARENT_CHILD",
                joinColumns = @JoinColumn(name = "PARENT_ID"),
                inverseJoinColumns = @JoinColumn(name = "CHILD_ID")
        )
        private Child child;
    }
    
    @Entity
    public class Child{
        @Id
        @GeneratedValue
        @Column(name = "CHILD_ID")
        private Long id;
        private String name;
        
        @OneToOne(mappedBy = "child")
        private Parent parent;
    }
}
```

일대일이니까 OneToOne 애노테이션을 사용해서 표시해줬고,

부모 엔티티에서 @JoinTable을 통해서 표시함

* name : 매핑할 조인 테이블의 이름
* joinColumns : 현재 엔티티를 참조하는 외래 키
* inverseJoinColumns : 반대 방향 엔티티를 참조하는 외래키

####

####

#### 일대다 조인 테이블

위의 예시를 토대로 일대다 관계를 만들기 위해서는 조인 테이블의 컬럼 중 다와 관련된 컬럼인 child에 유니크 제약 조건을 걸어야 한다.

```java
package com.example.jpa_project.ch07Example;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

public class JoinTable_OneToMany {
    @Entity
    public class Parent{
        @Id
        @GeneratedValue
        @Column(name = "PARENT_ID")
        private Long id;
        
        private String name;
        
        @OneToMany
        @JoinTable(
                name = "PARNET_CHILD",
                joinColumns = @JoinColumn(name = "PARENT_ID"),
                inverseJoinColumns = @JoinColumn(name = "CHILD_ID")
        )
        private List<Child> childs = new ArrayList<>();
    }
    
    @Entity
    public class Child{
        @Id
        @GeneratedValue
        @Column(name = "CHILD_ID")
        private Long id;
        
        private String name;
    }
}
```

####

####

#### 다대일 조인 테이블

일대다나 다대일이나 같기 때문에 일대다, 다대일 양방향을 하기 위해서는 요런식으로

```java
package com.example.jpa_project.ch07Example;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

public class JoinTable_ManyToOne {
    @Entity
    public class Parent{
        @Id
        @GeneratedValue
        @Column(name = "PARENT_ID")
        private Long id;

        private String name;

        @OneToMany(mappedBy = "parent")
        private List<Child> childs = new ArrayList<>();
    }

    @Entity
    public class Child{
        @Id
        @GeneratedValue
        @Column(name = "CHILD_ID")
        private Long id;
        
        private String name;

        @ManyToOne(optional = false)
        @JoinTable(
                name = "PARENT_CHILD",
                joinColumns = @JoinColumn(name = "CHILD_ID"),
                inverseJoinColumns = @JoinColumn(name = "PARENT_ID")
        )
        private Parent parent;
    }
}
```

#### 다대다 조인 테이블

다대다 관계를 만들기 위해서는 조인 테이블의 두 컬럼을 합해서 하나의 복합 유니크 제약조건을 걸어줘야 한다.

```java
package com.example.jpa_project.ch07Example;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

public class JoinTable_ManyToMany {
    @Entity
    public class Parent{
        @Id
        @GeneratedValue
        @Column(name = "PARENT_ID")
        private Long id;
        
        private String name;
        
        @ManyToMany
        @JoinTable(
                name = "PARENT_CHILD",
                joinColumns = @JoinColumn(name = "PARENT_ID"),
                inverseJoinColumns = @JoinColumn(name = "CHILD_ID")
        )
        private List<Child> childList = new ArrayList<>();
    }
    
    @Entity
    public class Child{
        @Id
        @GeneratedValue
        @Column(name = "CHILD_ID")
        private Long id;
        private String name;
    }
}
```

####

####

#### 엔티티 하나에 여러 테이블 매핑

@SecondaryTable을 사용하면 하나의 엔티티에 여러 테이블을 매핑할 수 있다네

근데 애초에 두 테이블을 하나의 엔티티에 매핑하는 것 보다는 테이블당 엔티티를 만드는 것이 국룰? 이기 때문에 굳이 이렇게 하지는 말자







