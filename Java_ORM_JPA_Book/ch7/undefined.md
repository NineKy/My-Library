# 임베디드 타입

_임베디드(Embedded) : 새로운 값 타입을 직접 정의해서 사용하는 것_

새로운 값 타입을 직접 정의한다고 해도 타입은 기본 타입처럼 사용한다.





예를 들어서 Member라는 객체에 Address라는 객체타입의 필드가 있는경우

Member

@Embedded : 값 타입을 사용하는 곳에 표시

사용하는 입장에서는 @Embedded을 활용

```java
@Entity
public class Member{
	@Id @GeneratedValue
	private Long id;

	@Embedded
	private Address address;
}
```

Address

@Embeddable : 값 타입을 정의하는 곳에 표시

사용 당하는 입장에서는 @Embeddable을 활용

```java
@Embeddable
public class Address{
	private String latitude;
	private String longitude;
}
```

**임베디드 타입을 포함한 모든 값 타입은 엔티티의 생명주기에 의존하기 때문에**

**엔티티와 임베디드 타입의 관계를 UML로 표현하자면 Composition 관계가 된다.**





## 임베디드 타입과 테이블의 매핑

→ 알아서 JPA에서 진행해주기 때문에 걱정할 필요 없다

_나중에 진짜 되는지 한번 보자_





## @AttributeOverride(속성 재정의)

위의 예시에 이어서 Member에 Address외에 companyAddress가 추가됬다고 가정해보자

```java
@Entity
public class Member{
	@Id @GeneratedValue
	private Long id;

	@Embedded
	private Address homeAddress;
	@Embedded
	private Address companyAddress;

}
```

만약에 이렇게 2개의 address가 존재하고 있는 상황에서 테이블과 매핑을 해버리면

address의 필드인 latitude, longitude 2개가 하나씩 중복되서 들어가게 된다.

→ 이러한 경우에 위 애노테이션을 사용해서 매핑 정보를 재정의해줘야함



이렇게

```java
@Entity
public class Member{
	@Id @GeneratedValue
	private Long id;

	@Embedded
	private Address homeAddress;
	@AttributeOverrides({
			@AttributeOverride(name="latitude", column=@Column(name = "COMPANY_LATITUDE")),
			@AttributeOverride(name="longitude", column=@Column(name = "COMPANY_LONGITUDE"))
	})
	private Address companyAddress;

}
```

그리고 만약 임베디드 타입이 null이면 해당 타입의 모든 필드 값들도 null!













