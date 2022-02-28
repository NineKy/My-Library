# 값 타입의 비교, 컬렉션

## 값 타입의 비교

자바에서 제공해주고 있는 객체 비교는 2가지이다.

동일성(identiy) 비교 : 인스턴스의 참조 값을 비교 ‘==’사용

동등성(equivalence) 비교 : 인스턴스의 값을 비교, equals()사용

객체 지향에서 배운 것을 생각해보면 2개의 객체를 ==(동일성)으로 비교하게 되면 아무리 필드가 같더라도 서로 다른 인스턴스이기 때문에 거짓이 나온다.

하지만 값 타입은 인스턴스가 다르더라도 그 내부의 값들이 같다면 같은 것으로 판단해야 한다.

따라서 값 타입을 비교하는데 있어서는 .equals(동등성)으로 비교해야 하며,

**꼭 값 타입 내부에서 .equals()를 재정의해야 한다 → 모든 필드의 값을 비교하도록**

\+equals함수를 재정의하면 해시를 사용하는 컬렉션인 hashCode함수도 같이 재정의해주자

\+ide에서 쉽게 재정의해준다..! 자동 삽입을 사용해서 처리하자





## 값 타입 컬렉션

만약에 값 타입을 하나 이상 저장하기 위해서는

컬렉션에 보관하고, @ElementCollection, @CollectionTable 을 사용하면 된다.

```java
@Entity
public class Member{
	@Id @GeneratedValue
	private Long id;

	@Embedded
	private Address homeAddress;

	@ElementCollection
	@CollectionTable(name = "FAVORITE_FOODS",
			joinColumns = @JoinColumn(name = "MEMBER_ID"))
	@Column(name="FOOD_NAME")
	private Set<String> favoriteFoods = new HashSet<String>();

	@ElementCollection
	@CollectionTable(name = "ADDRESS", 
			joinColumns = @JoinColumn(name = "MEMBER_ID"))
	private List<Address> addressHistory = new ArrayList<Address>();
}
```

이렇게 값 타입 컬렉션을 사용하게 되면 영속성 전이와 고아 객체 제거 기능은 필수로 가지게 된다.

값 타입 컬렉션을 조회할 때도 fetch 전략을 사용하는 것이 가능한데, 디폴트는 LAZY이다.



### 값 타입 컬렉션 수정

```java
Member member = em.find(Member.class, 1L);

//임베디드값 수정
member.setHomeAddress(new Address("123","456"));
//만약 위의 member에서 address를 수정하게 되면 
//→ 일단 member에 매핑된 address를 수정하는 것 이기 때문에 member에게 update를 침

//기본값 타입 컬렉션 수정
Set<String> favoriteFoods = member.getFavoriteFoods();
favoriteFoods.remove("탕수육");
favoriteFoods.add("깐풍기");
//자바의 String타입을 수정이 불가하기 때문에 삭제하고 다시 넣어야함

//임베디드 값 타입 컬렉션 수정
List<Address> addressHistory = member.getAddressHistory();
addressHistory.remove(new Address("123", "456"));
addressHistory.add(new Address("1111", "2222"));
//값 타입또한 불변해야 하기 때문에 일단 삭제하고 다시새로 등록하는게 맞다
// 참고로 equals, hashcode구현
```

###

### 값 타입 컬렉션 제약사항

뭐 기존과 같은 맥락인데 이제는 컬렉션이기 때문에 컬렉션 안에 들어가있는 값 타입들은 별도의 테이블에 보관되게 된다. 그래서 만약 보관된 값 타입이 변경되게 되면 혼란스러워지겠다.

그래서 JPA 구현체들은 값 타입 컬렉션에 문제가 발생하면 해당 값 타입 컬렉션이 매핑되어 있는 모든 테이블을 삭제하고 다시 저장하게 된다.

그래서 컬렉션보다는 일대다 관계를 고려하는게... 더 좋은듯

차라리 일대다 관계를 만들어주고 따로 추가적으로 영속성 전이 + 고아 객체 제거 기능을 추가해주면 값 타입 컬렉션과 똑같게 사용할 수 있지 않나...













