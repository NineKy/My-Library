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

equals을 재정의하는데 있어서 고려해야하는 점은 몇 가지 있다 <br>
순서대로 반사성, 대칭성, 추이성, 일관성, Null이 아님이다 <br>
반사성은 거울이라고 생각하면 편하다 -> 객체가 자기 자신과 비교했을 때 같냐? 이것을 보고 반사성이라고 한다 <br>
<br>

대칭성부터는 두 객체를 비교하게 되는 것이다 -> A와 B가 있을 때 A.equals(B)의 결과와 B.equals(A)의 결과가 같아야 한다는 것이다 <br>
이게 실제로 구현하는 것에 따라서 다른게 나올 수도 있기 때문에 항상 주의해야 한다는 점이다 <br>
<br>

추이성은 3단 논법이라고 생각하면 편하다 -> A, B, C 가 있을 때 <br>
A.equals(B) 가 true이고 <br>
B.equals(C) 가 true 이면 <br>
C.equals(A) 가 true 이여야 한다는 것이다 <br>
강의에서 예시로는 Point 와 Point을 상속받아서 만든 ColorPoint 을 비교하는데 <br>
ColorPoint(1, 2, RED) , Point(1, 2), ColorPoint(1, 2, BLUE) <br>
이렇게 3개를 예시로 들어주었다 -> 만약에 equals의 구현이 Point에도 되어있으며, ColorPoint 에서는 super.equals와 colorType비교가 같이 들어가 있는 상태에서 
Point.equals(ColorPoint-RED), Point.equals(ColorPoint-BLUE) 는 super의 Point 비교를 사용하고, 이는 좌표만 확인하기 때문에 true가 나올 것이다 <br>
근데 ColorPoint-RED 와 ColorPoint-BLUE 이 같냐? 는 보기만 해도 아닌 것을 알고 이건 이상한 것이라고 볼 수 있다 <br>
실제로 ColorPoint.equals을 통해서 확인해보면 ColorType을 비교하기 때문에 두 개는 다르다고 나올 것이고 이것은 추이성을 지키지 못한 케이스라고 볼 수 있다 <br> 
여기서 잠깐 보면 Point.equals 에서는 Point 이라는 타입을 가지고 비교하기 때문에 1, 2으로만 비교할 수 있었던 건데 하위 타입은 하위 타입도 비교해주는 것이 필요하지 않을까? <br>
이런 생각을 하면서 super에 있는 equals 에 Object.getClass()을 통해서 타입을 확인해준다면, 이건 라스코프 치환 원칙을 위배한 것이다 <br>
라스코프 치환 원칙은 상위(부모) 타입으로 동작하는 코드가 있을 때, 상위 코드를 상속받는 자식 클래스의 타입을 가지고 넣어줬을 때 동일하게 동작해야한다 <br>
즉, 부모클래스 대신 자식클래스가 들어가도 코드는 동일하게 동작해야 한다는 의미이다 <br>
<br>
