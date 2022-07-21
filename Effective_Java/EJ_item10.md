# equals는 일반 규약을 지켜 재정의하라
일반적으로 object 에서 override 해서 재정의할 수 있는 대표적인 함수는 이와 같다 <br>
equals, toString, hashcode, clone, finalize <br>
<br>

equals 는 대부분 인텔리제이에게 해줘 해서 만드는 경우가 많은데, 너무 생각 없이 만드는 것 보다는 다음과 같은 이유가 아닌 경우에 생성해서 사용하자 <br>
우선적으로 인스턴스가 싱글톤으로 고유할 때이다 -> 인스턴스가 고유하면 굳이 equals 을 사용할 이유가 전혀 없지요? <br>
<br>

다음으로는 인스턴스의 '논리적 동치성'을 검사할 필요가 없을 때 <br>
논리적 동치성이란 어떠한 객체의 내부가 같으냐를 비교하는 의미이다 <br>
내용이 같다면 똑같은 객체로 보겠다는 의미 = 논리적 동치성 <br>
예시를 들면, String 이 있다 - String이 "hello" 이면 또 다른 String이 "hello" 이면 이것을 같다고 보는 것과 같은 논리이다 <br>
<br>

다음으로는 상위 클래스에 이미 정의 되어 있는 경우 <br>
에는 따로 equals을 정의할 필요가 없다 -> 예를 들면 hashset이나 list이 있다 <br>
<br>

마지막으로는 클래스가 private 이거나 package-private 이고 equals 메소드를 호출할 일이 없을 때 이다 <br>
public 한 클래스는 equals 가 호출되지 않을 것이라는 보장을 하는 것이 어렵다... <br>
public은 아무나 참조해서 사용하는 것이 가능하기 때문에 만약 참고해서 사용하면서 자연스럽게 List 나 Set에 넣어버리면 equals가 호출되어 버린다 <br>
그 의미는 이렇게 어떻게 쓰일지 모르기 때문에 private 에서 우리가 직접 equals을 호출할 일이 없는 클래스라면 재정의하지 말자는 의미이다 <br>
<br>

그러면 어떻게 equals를 재정의해야하는지 한 번 보자 <br>
<br><br>
