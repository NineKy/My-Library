# 연관관계 매핑 기초

엔티티들은 다른 엔티티와 연관관계가 많이 이루어진다. 객체 같은 경우에는 참조라는 주소를 이용해서 다른 객체와 관계를 맺지만 테이블 같은 경우에는 외래 키를 사용해서 다른 테이블과 관계를 맺는다. 이처럼 객체와 테이블은 다르게 참조하고 있는데, 이렇게 객체의 연관관계랑 테이블의 연관관계를 연결? 동기화? 시키는 것이 매핑이라고 볼 수 있고 이것이 목표이다.

### 연관관계 매핑에서 중요한 키워드

* 방향
  * 단방향, 양방향이 있다. 단방향은 한쪽만 참조하고 있는 경우를 의미하고 양방향은 양쪽 모두가 서로를 참조하는 관계이다.
  * 말 그대로의 관계들이고, 방향이라는 키워드는 사실 객체관계에만 존재하고 있으며, 테이블의 관계는 항상 양방향 관계로 이루어져 있다.
* 다중성
  * 연관관계를 맺을 때, 사용 횟수?라고 하긴 좀 애매하지만 각 연관관계에서 한 번만 참조하느냐 여러번 참조하느냐를 보여준다.
  * 일대일, 다대일, 일대다, 다대다 이렇게 종류가 있다.
* 연관관계의 주인
  * 양방향 관계 상황에서 연관관계의 주가 되는 엔티티를 연관관계의 주인이라고 부른다.

### 단방향 연관관계

일단은 다대일에 대해서 알아봐야한다.

진행을 위한 예시

* 회원과 팀이 있다.
* 회원은 하나의 팀에만 소속될 수 있다.
* 회원과 팀은 다대일 관계이다.

구조는 이렇다.

```java
Member
- id, username, Team

Team
- id, name
```

```java
Member
- MEMEBER_ID, TEAM_ID, USERNAME

Team
- TEAM_ID, NAME
```

이 예시를 객체 연관관계와 테이블 연관관계부터 시작

* 객체 연관관계
  * Member 객체는 member의 team 필드로 Team객체와 연관관계를 맺는다.
  * Member 객체와 Team 객체는 단방향 관계인데, Member 객체의 필드 Team을 통해서 Member에서는 Team에 대한 정보를 가지고 있어야 하지만, Team객체는 어떠한 Member를 가지고 있는지 저장하는 필드가 없기 때문에 단방향 관계라고 볼 수 있다.
*   테이블 연관관계

    * TEAM\_ID를 통해서 Member 객체와 Team 객체가 연결이 된다.
    * 위에서 말했듯이, 테이블 간의 방향은 양방향 관계이다.
    * 이렇게 서로 조인을 해줘야 한다

    ```java
    SELECT * FROM MEMBER M
    JOIN TEAM T ON M.TEAM_ID = T.TEAM_ID

    SELECT * FROM TEAM T
    JOIN MEMBER M ON T.TEAM_ID = M.TEAM_ID
    ```

    * 그리고 조금 더 정확하게 보면 이건 양방향이라고 보기보다는 서로 다른 단방향이 이어져 있다고 볼 수 있다.

그러면 객체 연관관계와 테이블 연관관계를 정리해보자

객체는 참조를 통해서 연관관계를 이루고, 테이블은 외래 키를 통해서 연관관계를 이룬다

예를 들면 참조를 사용하는 객체는 get을 사용해서 값을 가져오지만 테이블은 조인을 사용해서 가져온다

객체의 참조는 단방향으로 볼 수 있지만 외래키를 사용하는 테이블의 연관관계를 양방향이라서 A JOIN B가 가능하면 B JOIN A 이렇게 사용할 수 있다.

#### 순수한 객체 연관관계

객체 단위에서 사용하는 걸 보자

```java
@Data
public class Member{
	private String id;
	private String username;

	private Team team;
}

@Data
public class Team{
	private String id;
	private String name;
}
```

이렇게 값들이 있다면 member에서 team을 연동해서 사용하는 방법은

```java
Member mem = new Member();
Team team = mem.getTeam();
```

요렇게 사용하게 된다. 이렇게 객체를 참조해서 연관관계를 사용하는 것을 **객체 그래프 탐색** 이라고 한다

#### 테이블 연관관계

SQL단위(데이터베이스) 에서 회원과 팀 관계를 형성하는 것을 확인해보자

```sql
CREATE TABLE MEMBER(
	MEMBER_ID VARCHAR(255) NOT NULL,
	TEAM_ID VARCHAR(255),
	USERNAME VARCHAR(255),
	PRIMARY KEY (MEMBER_ID)
);

CREATE TABLE TEAM(
	TEAM_ID VARCHAR(255) NOT NULL,
	NAME VARCHAR(255),
	PRIMARY KEY (TEAM_ID)
);

ALTER TABLE MEMBER ADD CONSTRAINT FK_MEMBER_TEAM 
FOREIGN KEY (TEAM_ID) REFERENCES TEAM
```

이렇게 member table을 생성하고, team table을 생성하고 foreign key의 등록을 통해서 연관관계를 맺어주는데 데이터 베이스에서는 이것을 **조인**이라고 부른다

#### 객체 관계 매핑

자 이렇게 귀찮게 서로서로 해줘야하는 부분을 제외하고 한 번에 할 수 있는 방법이 있다

```java
@Data
@Entity
public class Member {

    @Id
    @Column(name = "MEMBER_ID")
    private Long id;

    private String username;

    @ManyToOne
    @JoinColumn(name = "TEAM_ID")
    private Team team;

}
```

```java
@Data
@Entity
public class Team {
    @Id
    @Column(name = "TEAM_ID")
    private String id;

    private String name;
}
```

이제 연관관계를 맺었는데 사용한 애노테이션은 @ManyToOne 그리고 @JoinColumn을 사용했다.

* @ManyToOne : 이름 그대로 다대일 이라는 관계를 나타내는 매핑 정보이다. 회원과 팀은 다대일 관계이고, 연관관계를 매핑할 때 이렇게 다중성을 나타내는 애노테이션을 필수로 붙혀줘야 한다.
  * optional : false로 설정하면 연관된 엔티티가 항상 있어야 한다
  * fetch : fetch 전략을 설정요건 이후에 자세하게 다룬다
    * @ManyToOne = FetchType.EAGER
    * @OneToMany = FetchType.LAZY
  * cascade : 영속성 전이 기능을 사용 이후에 자세하게 다룸
  * targetEntity : 연관된 엔티티의 타입 정보를 설정(처음봄=거의안씀)
* @JoinColumn : 조인 컬럼은 외래 키를 매핑할 때 사용한다. 여러가지 속성들도 있다
  * name : 이 속성은 매핑할 외래 키 이름을 지정해주는 단계이다. 생략가능하긴하다 - 기본값은 필드명\_참조하는 테이블의 기본 키 컬럼명
  * referencedColumnName : 외래 키가 참조하는 대상 테이블의 컬럼명 - 참조하는 테이블의 기본키 컬럼명
  * foreignKey(DDL) : 외래 키 제약조건을 직접 지정할 수 있다. 이 속성은 테이블을 생성할 때만 사용
  * unique, nullable, insertable, updatable, columnDefinition, table 이 있는데 이런건 @Column 애노테이션과 같음

#### 양방향 연관관계

기존의 단방향은 회원에서 팀으로만 접근하는 방식이였지만 반대로 팀에서도 회원에 접근할 수 있도록 팀 객체에 List형태로 Member들을 넣어주자 → 그럼 Team 객체에서 수정만 해주면 되겠지요?

```java
package EntityMappingExample;

import lombok.Data;

import javax.persistence.*;
import java.util.List;

@Data
@Entity
public class Team {
    @Id
    @Column(name = "TEAM_ID")
    private String id;

    private String name;

    @OneToMany(mappedBy = "team")
    private List<Member> members;
}
```

양방향 관계를 위해서 List형태로 Member를 뭉텅이로해서 가지고 있다. 그리고 @OneToMany 애노테이션을 사용했고 mappedBy속성으로 반대쪽 매핑 필드 이름값으로 주었다

근데 왜 mappedBy 속성을 넣어줘야 하는 걸까?

**객체에는 양방향 연관관계라는 것이 존재하지 않는다** 단지 각자의 단방향을 서로 이어놨기 때문에 그렇게 보이는 것 뿐이다.

그러면 연관관계가 있는 테이블은 외래 키 하나로 두 테이블의 연관관계를 관리하는데, 여기서 서로 참조하는 것이 2가지씩 있는 상황인데, 그러면 **두 객체의 연관관계 중 하나를 정해서 테이블의 외래키를 관리**해야 하는데, 이것을 **연관관계의 주인**이라고 한다

그 연관관계의 주인만이 **데이터 베이스 연관관계와 매핑되고 외래키를 관리한다(CRUD)** 주인이 아닌 쪽은 단지 **R**만 가능하다 ⇒ **연관관계의 주인을 정한다 = 외래 키 관리자를 정한다**

위의 예시로 보자면

\-만약 Member 엔티티에 있는 Member.team을 주인으로 선택하면 자기 테이블에 있는 외래 키를 관리하면 됨

\-하지만 Team에 있는 team.members를 주인으로 선택하게 되면 외부에 있는 테이블의 외래 키를 관리해야함

그러면 결과적으로 지금 Member 엔티티이 외래 키를 가지고 있음으로 Member.team이 주인이라고 볼 수 있음

**양방향 관계에서 값을 넣어줄 때 주인이 아닌 방향에 값을 설정하지 않아도 외래 키 값이 정상적으로 적용**

\-반대로 주인이 아닌 곳에만 값을 넣어주게 된다면 정상적으로 값이 저장되지 않는 다는 점을 기억하자

\-이게 DB적이 아니라 객체적으로 봐도 사실은 **양쪽 방향에 모두 값을 입력해주는게 안전**하다 - **걍 이렇게해**

내용 정리

1. 단방향 매핑만으로 테이블과 객체의 연관관계 매핑은 된거임
2. 단방향을 양방향으로 만들어주면 반대 방향으로 객체 탐색 기능이 추가됨
3. 양방향 연관관계를 매핑하려면 객체에서 양방향을 모두 관리해야함
4. 연관관계의 주인은 외래 키의 위치에 따라서 정하는거다 - 비즈니스에 따른게 아님

\
\
\
\
\
\
\
\
\
\
