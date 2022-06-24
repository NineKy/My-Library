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


## 단점

상속을 하려면 public, protected를 사용해야하는데, 생성자가 없고 정적 팩토리 메소드만 존재하면 상속이 불가능 <br>
<br>

정적 팩토리 메소드만 사용하게 되면 → 자바독스와 같은 것으로 분석하는 경우에는 찾기가 힘들어진다 <br>
<br>

그래서 제목에도 잘 나와있다 -> 정적 팩터리 메소드를 사용하는 것을 **고려하라** <br>








<br><br><br><br><br><br><br><br><br><br>