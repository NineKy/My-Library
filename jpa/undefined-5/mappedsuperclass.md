# @MappedSuperclass

## @MappedSuperclass

부모 클래스는 테이블과 매핑하지 않고 부모 클래스를 상속받는 자식 클래스에게 매핑 정보만 제공하고 싶으면 @MappedSuperclass를 사용한다.

@Entity와 다른 점을 보자면 @Entity는 진짜 테이블에 매핑되는 부분이지만 @MappedSuperclass 는 실제 테이블과 매핑되지는 않는 다는 점이고 단순히 매핑 정보를 상속할 목적으로만 사용된다.

사용되는 예시를 보면

```java
package com.example.jpa_project.ch07Example;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.persistence.MappedSuperclass;

public class MappedSuperclassMapping {

    @MappedSuperclass
    public abstract class BaseEntity{
        @Id
        @GeneratedValue
        private Long id;

        private String name;
    }

    @Entity
    public class Member extends BaseEntity{
        private String email;
    }

    @Entity
    public class Seller extends BaseEntity{
        private String shopName;
    }
}
```

각 Member와 Seller는 BaseEntity에 있는 id, name을 공통적으로 가지고 있게 된다.

BaseEntity에는 객체들이 주로 사용하는 공통 매핑 정보를 정의했고 자식 엔티티들은 상속을 통해서 BaseEntity에 있는 매핑 정보를 물려받게 되었다.

만약에 그대로 사용하기 싫고 컬럼을 재정의를 해줘야 하는 경우에는 @AttributeOverrides, @AttributeOverride를 사용하고,

연관관계를 재정의 하기 위해서는 @AssociationOverrides, @AssociationOverride를 사용한다.

정리해보면

* @MappedSuperclass는 테이블과 매핑되지 않고 자식 클래스에 엔티티의 매핑 정보를 상속하기 위해 사용
* @MappedSuperclass로 지정한 클래스는 엔티티가 아니기 때문에 em.find, JPQL로 검색이 불가능
* 이 클래스를 직접 생성해서 사용할 일은 없을 예정이기 때문에 추상 클래스로 생성







