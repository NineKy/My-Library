# 복합 키와 식별 관계 매핑

## 복합 키와 식별 관계 매핑

#### 식별 관계 VS 비 식별 관계

단순하게 생각해보면 외래 키가 기본 키에 포함되고 있는지에 대한 여부에 따라서 구분된다.

식별 관계 - 식별 관계는 부모 테이블의 기본 키를 내려받아서 자식 테이블의 기본 키 + 외래 키로 사용하는 관계이다

비식별 관계 - 부모 테이블의 기본 키를 받아서 자식 테이블에서 단지 외래 키로만 사용하는 관계

* 필수적 비식별 관계 - 외래 키에 null을 허용하지 않는 경우이기 때문에 연관관계를 필수적으로 맺어야함
* 선택적 비식별 관계 - 외래 키에 null을 허용한 경우이기 때문에 연관관계를 맺든 안맺는 선택

복합 키에서 비식별 관계를 매핑하는 방법

단순하게 기본 키가 하나면 @Id를 사용해서 적용해주면 되지만, 만약에 2개 이상으로 기본 키가 이루어진 복합 기본 키인 경우에는 @Id를 2개 써야하나? 근데 당연히 에러가 나오겠지여

일단 JPA는 영속성 컨텍스트에 엔티티를 보관할 때 엔티티의 식별자를 키로 사용한다. 그리고 그 식별자를 구분하기 위해서 사용하는 것이 equals, hashCode 이렇게 2가지를 사용해서 동등성을 비교한다. 자 그럼 만약에 식별자 필드가 2개 이상이면 어떻게 해야하나 → 당연히 equals, hashCode를 새로 구현을 해줘야한다.

그럼 JPA에서 지원해주는 복합 키 구현 방식으로는 → @IdClass, @EmbeddedId 이렇게 2가지의 방법을 제공해주고 있다.





#### @IdClass

이건 그래도 관계형 데이터베이스에 가까운 방법

```java
package com.example.jpa_project.ch07Example;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.IdClass;
import java.io.Serializable;
import java.util.Objects;

public class MultiId_IdClass {

    @Entity
    @IdClass(ParentId.class)
    public class Parent{
        @Id
        @Column(name = "PARNET_ID")
        private String id;

        @Id
        @Column(name = "PARENT_ID2")
        private String id2;
    }

    public class ParentId implements Serializable{
        private String id1;
        private String id2;

        public ParentId() {
        }

        public ParentId(String id1, String id2) {
            this.id1 = id1;
            this.id2 = id2;
        }

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;
            ParentId parentId = (ParentId) o;
            return Objects.equals(id1, parentId.id1) && Objects.equals(id2, parentId.id2);
        }

        @Override
        public int hashCode() {
            return Objects.hash(id1, id2);
        }
    }
}
```

@Id를 모든 복합 기본 키에 붙혀주고 대신 엔티티 클래스에 @IdClass를 붙혀주되,

식별자 클래스(ParentId)를 만들어주어야 한다. 이 식별자 클래스에는 충족해야 하는 조건들이 존재함

* 식별자 클래스의 속성명과 엔티티에서 사용하는 식별자의 속성명이 같아야 한다.
* Serializable 인터페이스를 구현해야 한다.
* equals, hashCode를 구현해야 한다.
* 기본 생성자가 있어야 하고
* 클래스의 접근 타입은 public 이어야 한다.



이렇게 만들고 자식 클래스

```java
package com.example.jpa_project.ch07Example;

import javax.persistence.*;

public class MultiId_IdClass_Child {
    @Entity
    public class Child {
        @Id
        private String id;

        @ManyToOne
        @JoinColumns({
                @JoinColumn(name = "PARENT_ID1", referencedColumnName = "PARENT_ID1"),
                @JoinColumn(name = "PARENT_ID2", referencedColumnName = "PARENT_ID2")
        })
        private MultiId_IdClass_Parent.Parent parent;
    }
}
```

부모 테이블의 기본 키가 복합 키이기 때문에 자식 테이블의 외래 키도 복합키로 가져와야 하고 이렇게 가져오는 방식은 @JoinColumns으로 매핑해주고 내부에는 @JoinColumn으로 구현해준다. 만약 @JoinColumn의 name속성과 referencedColumnName이 같다면 뒤의 referencedColumnName은 생략할 수 있다.







#### @EmbeddedId

이건 객체지향에 가까운 방법

```java
package com.example.jpa_project.ch07Example;

import javax.persistence.Column;
import javax.persistence.Embeddable;
import javax.persistence.EmbeddedId;
import javax.persistence.Entity;
import java.io.Serializable;
import java.util.Objects;

public class MultiId_EmbeddedClass_Parent {
    
    @Entity
    public class Parent{
        @EmbeddedId
        private ParentId id;
        
        private String name;
    }
    
    @Embeddable
    public class ParentId implements Serializable {
        @Column(name = "PARENT_ID1")
        private String id1;
        @Column(name = "PARENT_ID2")
        private String id2;

        public ParentId() {
        }

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;
            ParentId parentId = (ParentId) o;
            return Objects.equals(id1, parentId.id1) && Objects.equals(id2, parentId.id2);
        }

        @Override
        public int hashCode() {
            return Objects.hash(id1, id2);
        }
    }
}
```

진짜 단순하게 Parent에서 그냥 식별자 클래스로 적용해주고 @EmbeddedId을 붙혀준다.

그리고 @Embeddable으로 해당 아이디를 받아서 구현해줘야 한다. 구현할 때 조건

* 구현해야 하는 클래스에 @Embeddable을 붙히자
* Serializable 인터페이스를 구현
* equals, hashCode를 구현
* 기본 생성자 필요
* 식별자 클래스는 public이여야 함





**equals(), hashCode()**

영속성 컨텍스트는 엔티티의 식별자로 키를 사용해서 엔티티를 관리한다. 그리고 해당 식별자를 비교할 때 equals(), hashCode()를 사용해서 비교하게 된다. 만약 이것을 정의하지 않게 되면 구별이 불분명해져 엔티티에 대한 접근에 문제가 생기게 된다.

그리고 식별자 클래스의 equals()와 hashCode()는 모든 필드를 사용해서 구현한다.







#### 여기서 만약 하나의 자식이 더 추가된다면...?

@IdClass

```java
package com.example.jpa_project.ch07Example;

import javax.persistence.*;
import java.io.Serializable;
import java.util.Objects;

public class MultiId_IdClass_Family {
    @Entity
    public class Parent{
        @Id
        @Column(name = "PARENT_ID")
        private String id;

        private String name;
    }

    @Entity
    @IdClass(ChildId.class)
    public class Child{
        @Id
        @ManyToOne
        @JoinColumn(name = "PARENT_ID")
        public Parent parent;
        
        @Id
        @Column(name = "CHILD_ID")
        public String childId;
        
        private String name;
    }

    public class ChildId implements Serializable{
        private String parent;
        private String childId;

        public ChildId() {
        }

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;
            ChildId childId1 = (ChildId) o;
            return Objects.equals(parent, childId1.parent) && Objects.equals(childId, childId1.childId);
        }

        @Override
        public int hashCode() {
            return Objects.hash(parent, childId);
        }
    }
    
    @Entity
    @IdClass(GrandChildId.class)
    public class GrandChild{
        @Id
        @ManyToOne
        @JoinColumns({
                @JoinColumn(name = "PARENT_ID"),
                @JoinColumn(name = "CHILD_ID")
        })
        private Child child;
        
        @Id
        @Column(name = "GRANDCHILD_ID")
        private String id;
    }
    
    public class GrandChildId implements Serializable{
        private ChildId child;
        private String id;

        public GrandChildId() {
        }

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;
            GrandChildId that = (GrandChildId) o;
            return Objects.equals(child, that.child) && Objects.equals(id, that.id);
        }

        @Override
        public int hashCode() {
            return Objects.hash(child, id);
        }
    }
}
```

자식의 자식같은 경우에도 구현은 같지만 항상 자신보다 바로 하나 위의 부모의 키를 가져와서 구현해야 한다는 점? 이정도? 구현자체는 비슷하다고 본다.



@EmbeddedId의 구성으로 확인

구조는 같지만 @MapsId를 사용하는 점이 다른점이라고 보인다.

```java
package com.example.jpa_project.ch07Example;

import javax.persistence.*;
import java.io.Serializable;
import java.util.Objects;

public class MultiId_EmbeddedClass_Family {
    
    @Entity
    public class Parent{
        @Id
        @Column(name = "PARENT_ID")
        private String id;
        private String name;
    }
    
    @Entity
    public class Child{
        @EmbeddedId
        private ChildId id;
        
        @MapsId("parentId")
        @ManyToOne
        @JoinColumn(name = "PARENT_ID")
        public Parent parent;
        
        private String name;
    }
    
    @Embeddable
    public class ChildId implements Serializable{
        private String parentId;
        
        @Column(name = "CHILD_ID")
        private String id;

        public ChildId() {
        }

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;
            ChildId childId = (ChildId) o;
            return Objects.equals(parentId, childId.parentId) && Objects.equals(id, childId.id);
        }

        @Override
        public int hashCode() {
            return Objects.hash(parentId, id);
        }
    }
    
    @Entity
    public class GrandChild{
        @EmbeddedId
        private GrandChildId id;
        
        @MapsId("childId")
        @ManyToOne
        @JoinColumns({
                @JoinColumn(name = "PARENT_ID"),
                @JoinColumn(name = "CHILD_ID")
        })
        private Child child;
        
        private String name;
    }
    
    @Embeddable
    public class GrandChildId implements Serializable{
        private ChildId childId;
        
        @Column(name = "GRANDCHILD_ID")
        private String id;

        public GrandChildId() {
        }

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;
            GrandChildId that = (GrandChildId) o;
            return Objects.equals(childId, that.childId) && Objects.equals(id, that.id);
        }

        @Override
        public int hashCode() {
            return Objects.hash(childId, id);
        }
    }
}
```

기존과의 차이는 @MapsId를 @Id대신에 넣어줬다는 것이다. @MapsId는 외래키와 매핑한 연관관계를 기본 키에다가도 매핑하겠다는 의미이다.

비식별 관계로 구현하는건 더더더더더욱 단순한다. 단순하게 Serializable을 가지고 있는 복합 키 클래스를 만들지 않아도 매핑이 가능하다는 점..!







#### 그래서 식별, 비식별 관계의 장단점

결과적으로는 비식별 관계를 많이 사용한다네요 왜와이

* 식별관계는 자식 테이블로 갈 수록 기본 키 컬럼이 점점 늘어나면 사고가 되어간다.
* 식별 관계는 2개 이상의 컬럼을 합해서 복합 기본 키를 만들어야 하는 경우가 있다.
* 식별 관계를 맺어두면 나중에 변경사항이 있을 때 힘들다는점?
* 식별 관계는 자식 테이블에서 부모 테이블의 기본 키를 가져와서 사용하기 때문에 테이블 구조가 유연하지 않다.

이렇게나 단점이 많으면 그럴만도하다

그럼 왜 비식별 관계를 더 많이 사용하냐

* 그냥 식별 함수를 만들지 않아도 괜찮기 때문에 무조건 단순하니까
* 비식별 관계의 기본 키는 주로 대리 키를 사용하게 되는데, 여기서는 JPA에서는 @GeneratedValue를 사용해서 대리 키를 많이 사용하기 때문에 편리하게 사용할 수 있다. - 대부분 Long 타입의 대리 키를 사용한다.









