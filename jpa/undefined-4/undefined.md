# 다대일, 일대다 관계

### 다대일

다대일↔일대다 이러한 구조를 가지고 있으며

**항상 다쪽에 외래키가 있다는 점은 알아 두자**

다쪽에 외래키가 있다 === 다쪽이 연관관계의 주인이구나!



#### 다대일 단방향

기존의 Team, Member가지고 생각해보자

Member

```java
package com.example.jpa_project.ch06Example;

import javax.persistence.*;

@Entity
public class Member {
    @Id
    @GeneratedValue
    @Column(name = "MEMBER_ID")
    private Long id;

    private String username;

    @ManyToOne
    @JoinColumn(name = "TEAM_ID")
    private Team team;
}
```

Team

```java
package com.example.jpa_project.ch06Example;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Entity
public class Team {
    @Id
    @GeneratedValue
    @Column(name = "TEAM_ID")
    private Long id;

    private String name;
}
```

자.. 뭐 그렇게 어려운 부분은 없지만 항상 다시 잊지 말아야 할 것 :

다대일 단방향시에는 단일로 가지고 있는 곳(Member)에서 Team 필드를 넣어주고 @ManyToOne을 넣어주자

다양한데 이 곳(Member)에서는 하나로 가지고 있다 = ManyToOne (이렇게 암기해보자)



#### 다대일 양방향

기존의 단방향의 예시에서는 단순하게 Member측에서 Team필드를 @ManyToOne + @JoinColumn(name=”외래키”) 이러한 방식으로 넣어뒀다.

하지만 양방향이기 때문에 Team측에서도 연관되어 있는 Member의 값들을 가지고 있어야한다.

Member

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

    @ManyToOne
    @JoinColumn(name = "TEAM_ID")
    private Team team;

    public void setTeam(Team team){
        this.team = team;

        if(!team.getMembers().contains(this)){
            team.getMembers().add(this);
        }
    }
}
```

Team

```java
package com.example.jpa_project.ch06Example;

import lombok.Data;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Entity
@Data
public class Team {
    @Id
    @GeneratedValue
    @Column(name = "TEAM_ID")
    private Long id;

    private String name;

    @OneToMany(mappedBy = "team")
    private List<Member> members = new ArrayList<>();

    public void addMember(Member member){
        this.members.add(member);
        if(member.getTeam() != this){
            member.setTeam(this);
        }
    }
}
```

이제는 Team측에서도 자신의 이름을 가지고 있는 Member들을 가지고 있어야 하는데, 분명 다수의 Member가 나올 것이기 때문에 List로 구현하고 @OneToMany를 넣어주고 mappedBy속성에 매핑되는 이름을 적어줌

정리해보면

**양방향은 외래 키가 있는 쪽이 연관관계의 주인이다**

일대다와 다대일 연관관계에는 다쪽에 외래키가 존재하고 있다.

Team과 Member을 두고 보았을 때는

* Member가 다쪽이고
* Member에서 JoinColumn(name=”디비필드이름”) 이렇게 외래키를 보유하고 있음

⇒ Member.Team을 보고 연관관계의 주인이라고 볼 수 있다.

연관관계의 주인(Member.Team)은 - 외래 키를 관리할 때 사용하고

나머지(Team.Members)은 - 조회를 위한 JPQL이나 객체 그래프를 탐색할 때 사용

**양방향 연관관계는 항상 서로를 참조해야 한다**

양방향 연관관계랑 애초에 어느 한 쪽만 참조하게 되면 조건이 만족되지 않는다.

항상 서로를 참조하기 위해서는 연관관계 편의 메소드를 작성하는 것이 좋은데, 위 예시의 setTeam(), addMember()가 대표적인 예시이다.

편의 메소드는 한 곳에만 작성하거나, 양쪽 다 작성할 수 있는데, 양쪽 다 작성시에는 무한 루프를 조심해야한다.





### 일대다

일대다 관계는 다대일 관계의 반대이다. 일대다 관계는 엔티티를 하나 이상 참조하기 때문에 자바 컬렉션(Collection, List, Set, Map) 중 하나를 사용하는 점!



#### 일대다 단방향

음.. 다대일 양방향의 상황과 비슷한데

하나의 팀에서 여러 회원을 참조하고 반대로 회원이 팀을 참조하지 않는다면 이런 것을 보고 단방향이라고 한다.

조금은 특이하게 기존에 다대일에서는 Member에서 @JoinColumn(name=디비필드이름”)이렇게 외래키를 처리해주고 있었지만

일대다의 경우에는리스트로 가지고 있는 측에서 @JoinColumn 애노테이션을 통해서 값을 추가해줘야 한다.

Team

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

    @ManyToOne
    @JoinColumn(name = "TEAM_ID")
    private Team team;
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
public class Team {
    @Id
    @GeneratedValue
    @Column(name = "TEAM_ID")
    private Long id;

    private String name;

    @OneToMany
    @JoinColumn(name = "TEAM_ID")
    private List<Member> members = new ArrayList<>();
}
```

일대다 단방향 매핑의 단점은 매핑한 객체가 관리하는 외래 키가 다른 테이블에 있다는점이 단점이다. 그래서 JPA에서 생성해주는 SQL을 확인해보면, 단순히 insert 뿐만아니라 굳이 연관관계의 표현을 위해서 update문 까지 생성한다네요!!

**일대다 단방향 매핑 보다는 다대일 양방향 매핑을 사용하자**

일대다 단방향 매핑을 사용하게 되면 매핑한 테이블 외에도 다른 테이블의 외래 키도 관리해줘야 한다. 위에서 하면서도 느꼈지만 엔티티에서만 살짝 수정만 해주면 다대일 양방향으로 사용할 수 있음으로 수정해서 사용하자...!



#### 일대다 양방향

웃기지만 일대다 양방향은 존재하지 않고 일대다 양방향=다대일 양방향 이렇게 볼 수 있다.

양방향 매핑에서 @OneToMany는 연관관계의 주인이 될 수 없다.

위에서 입이 닳도록 이야기 했지만, 항상 다쪽에 외래키가 들어가게 되는데, 그렇기 때문에 @OneToMany와 @ManyToOne 둘 중에 연관관계의 주인은 항상 다 쪽인 @ManyToOne을 사용한 곳이다.

그렇기 때문에 @ManyToOne에는 mappedBy 속성이 없다는 것을 확인할 수 있다.

대충 좋지 않다는 의미 같은데... 이걸 구현하는 것도 불가능한 것은 아니다.

→ 일대다 단방향 매핑 반대편에 같은 외래 키를 사용하는 다대일 단방향 매핑을 읽기 전용으로 하나 추가할 수 있다네요

Member

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

    @ManyToOne
    @JoinColumn(name = "TEAM_ID", insertable = false, updatable = false)
    private Team team;
}
```

Team

```java
**package com.example.jpa_project.ch06Example;

import lombok.Data;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Entity
@Data
public class Team {
    @Id
    @GeneratedValue
    @Column(name = "TEAM_ID")
    private Long id;

    private String name;

    @OneToMany
    @JoinColumn(name = "TEAM_ID")
    private List<Member> members = new ArrayList<>();
}**
```

특이한 점은 Member에서도 키를 관리하는 점인데... 이렇게 되면 서로 하나의 키를 관리하고 있기 때문에 문제가 발생할 수 있으니까, 오직 읽기만 가능하도록 updatable, insertable 옵션을 추가해주었다.

결국 이것을 구현한 방법은 일대다 단방향의 반대에 **다대일 단방향을 읽기 전용으로** 추가한 것이기 때문에 결국은 단점이 더 많아질 수 밖에 없다는 점을 기억해두고 그냥 다대일 양방향 매핑을 사용하는 방식을 고려하자





