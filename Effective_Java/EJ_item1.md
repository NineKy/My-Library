# 생성자 대신 정적 팩토리 메소드를 고려하라
간단하게 보면 이렇게 작성할 수 있음 

```java
public class myServcie{

	private String name;
	private boolean open;
	private boolean mine;

	public myService(){}

	public static isOpenedMyService(String name){
			myService ms = new myService();
			ms.name = name;
			ms.open = true;
			ms.mine = false;
	}

	public static isMineMyService(String name){
		myService ms = new myService();
		ms.name=  name;
		ms.open = false;
		ms.mine = true;
	}
}
```

## 장점

### 이름을 가질 수 있다
이름을 가짐으로써 어떠한 객체를 만들 것인지에 메소드의 이름에서 명시해줘서 사용하기 편리하게 유도하는 것이 가능하다 <br>
위의 코드에서 isOpenedMyService 메소드르 사용해서 open 되어 있는 서비스를 생성할 수 있으며 isMineMyService를 통해 mine인 서비스를 생성하는 것이 가능하며 <br>
해당 객체를 만드는데 있어서도 편하게 인지하고 사용하는 것이 가능하다 <br>
이름을 생성하는데 있어서 그래도 참고하면 타입? 느낌 <br>
from : 하나의 매개변수를 받아서 객체 생성 <br>
of : 여러개의 매개변수를 받아서 객체 생성 <br>
getInstance : 인스턴스를 생성, 있다면 있는걸 가져올 수도 있음 <br>
newInstance : 새로운 인스턴스를 생성 <br>

<br>

## 호출될 때 마다 인스턴스를 새로 생성하지 않아도 된다
static 으로 사용되기 때문에 따로 객체를 생성해서 사용할 필요없이 선언해둔 것을 통해서 사용하는 것이 가능 <br>
<br>

## 반환타입의 하위 타입 객체를 반환할 수 있다
이 장점을 통해서 구현체를 공개하지 않고도 그 객체를 원하는 타입별로 생성해내는 것이 가능하다 <br>
myService 이라고 하는 인터페이스가 존재하고 myService이라는 인터페이스를 구현받아서 만든 AService, BService, CService가 있다고 가정하자 <br>
이런 상황에서 myService이라는 클래스를 리턴하는 메소드를 만들고 내부 로직에 따라서 AService, BService, CService을 리턴하도록 구현해도 문제 없이 잘 된다 <br>
<br>

## 입력받은 매개변수에 따라서 다른 객체를 생성할 수 있다

## 정적 팩터리 메소드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다
책에서는 예시를 JDBC를 들었다 -> 실제 JDBC를 연동하는 코드를 구현해 놓았는데, 만약 다른 JDBC가 들어오면 코드를 바꿔야하나?  <br>
만약 정적 팩토리 메소드를 사용해서 구현을 해 두었으면 **내부코드에 의존적이지 않다는 것**이 장점이다 <br>
<br>

이외에도 DTO <-> Entity을 진행하는데 있어서 아주 유용하다고 한다 -> 이건 나도 생각했던건데 실제로 그렇다고 하니까 한번 적용해보자 <br>
//static으로 빼긴 뺐는데... 생성자도 한꺼번에 처리해버리고 싶지만 dto같은 경우에는 generator를 사용하기 때문에 이거쓰면 엔티티 전용으로밖애 못만들구나 <br>

## 단점

상속을 하려면 public, protected를 사용해야하는데, 생성자가 없고 정적 팩토리 메소드만 존재하면 상속이 불가능 <br>
<br>

정적 팩토리 메소드만 사용하게 되면 → 자바독스와 같은 것으로 분석하는 경우에는 찾기가 힘들어진다 <br>
<br>

그래서 제목에도 잘 나와있다 -> 정적 팩터리 메소드를 사용하는 것을 **고려하라** <br>


## Enum
이늄이라고도 부르고, 이건 상수 목록을 담을 수 있는 데이터 타입이다 <br>
이늄을 사용하게 되면 값을 지정해줄 때 선언해둔 값들로만 제한해서 사용하게 할 수 있다, 즉 Type-Safety를 보장해줄 수 있다 <br>
이것을 활용해서 싱글턴 패턴 구현에도 자주 사용하곤 한다 <br>
<br>

특정 enum 타입이 가질 수 있는 모든 값을 순회하며 출력하는 방법 -> Enum.values(); <br>
enum도 자바 클래스처럼 생성자, 메소드, 필드를 가질 수 있는가 <br>
enum을 비교하는데 있어서 ==을 사용해서 동일성을 비교하는 것이 가능한가 <br>
-> equals, == 모두 사용이 가능한데, equals이라는 메소드는 null-safe한 메소드가 아니기 때문에 null 체크를 거치고 확인해줘야하는데 == 을 사용하게되면 null에 대한 고민을 하지 않아도 되기 때문에 ==을 기본적으로 권장 <br>
<br>
enum을 키로 사용하는 map을 정의하거나 enum을 가지고 있는 set을 구현해보라 -> EnumMap, EnumSet을 사용 <br>
<br><br>

## 플라이웨이트 패턴
객체를 재사용하는 방법 중에 하나임 <br>

- 객체를 가볍게 만들어 메모리 사용을 줄이는 패턴
- 자주 변하는 속성 또는 외적인 속성과 변하지 않는 속성을 분리해서 재사용해서 메모리 사용을 줄이는 것이 가능 <br>
<br>

변경되지 않는 것을 플라이 웨이트 팩토리에 모아두고 가져와서 사용한다 <br>
<br><br>

## 인터페이스와 정적 메소드
자바 8, 9 이후부터는 인터페이스에 직접 메소드를 정의하는 것이 가능해졌다 <br>
default 키워드나 static 키워드가 들어가 있어야 메소드를 정의할 수 있다 <br>
default 메소드는 인터페이스에서 메소드의 선언 뿐만 아니라, 기본적인 구현체까지 제공하는 것이 가능하다 또한 기존의 인터페이스를 구현하는 클래스에 새로운 기능을 추가하는 것이 가능하다 <br>
static 메소드는 default에서는 불가능 했던(기본으로 public이기 때문에) private 메소드를 사용할 수 있다는 특징이 있다 <br>
<br><br>

## 서비스 제공자 프레임워크
서비스 제공자 인터페이스(Service Provider Interface), 서비스 제공자, 서비스 제공자 등록 API, 서비스 접근 API <br>
서비스 제공자 인터페이스란 그냥 원하는 서비스를 정의할 인터페이스를 지칭 <br>
구현체는 어디에 구현되어있던 상관은 없다 -> 중요한 건 구현체에 의존적이지 않게 되기 위해서이다 <br>
스프링부트에서도 빈이라는 개념을 통해서 서비스를 제공해주고, DI을 통해서 원하는 서비스를 가져와서 사용하는 것이 가능하다 <br>
빈등록을 통해서 서비스를 등록하고, getBean이나 @Autowired을 통해서 서비스를 가져와서 사용하는 것이 가능 <br>

자바를 기준으로 확인하는 방법으로는 ServiceLoader가 예시로 있다 <br>
<br>

브릿지 패턴의 예시를 잘 들어주셨는데, 롤로 따지면 스킨과 챔피언이 나누어져 있는 상황에서 만약 스킨이 추가되게 되면 모든 챔피언에도 적용이 되어야 하지만 <br>
챔피언과 스킨 사이에 브릿지, delegate클래스를 두고 해당에서만 작업해주면 원하는 스킨으로 바꿔끼면서 챔피언을 사용하는 것이 가능해진다 <br>
이 브릿지 패턴이 스프링의 PSA(Portable Service Abstraction)에도 적용되어있다는 점  <br>
<br>


## 리플렉션
클래스로더를 통해서 읽어온 클래스 정보를 사용하는 기술이다 <br>
옛날에 했던 것을 리마인드 해보면, 리플렉션을 통해서 객체를 생성하는 것이 가능했었으며, 이외에도 메소드나 필드의 이름을 가져오거나 메소드에 선언되어 있는 애노테이션등을 가져오는 것이 가능했다 <br>
옛날에 공부했던 링크 -> [요기](The_Java_Manipulation/리플렉션.md) <br>



<br><br><br><br><br><br><br><br><br><br>