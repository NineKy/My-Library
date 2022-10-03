# 연산자 오버로딩과 기타 관례
<br><br>

코틀린에서 어떤 언어 기능이 정해진 사용자 작성 함수와 연결하는 경우가 몇 가지 있다 <br>
근데 이런 언어 기능이 특정 타입과 연관되는 것보다는 특정 함수 이름과 연관되어 있다 예시로 어떤 클래스 안에 plus 
이라는 이름ㅇ의 메소드를 정의하면 그 클래스의 인스턴스에 대해서 + 연산자를 계산해주는 그러한 기능이다 <br>
이렇게 특정 언어에 미리 정해진 이름의 함수를 연결해주는 기법을 관례기법이라고 한다 <br>
이건 기존 자바 클래스에 대해 확장 함수를 구현하면서 관례에 따라 이름을 붙히면 기존의 자바 코드를 바꾸지 않아도 새로운 기능을 구여할 수 있는 그런 기능이다 <br>
<br><br>


### 산술 연산자 오버로딩
가장 단순한 예시이다. 자바에서는 int, long, char, string 등에서 +을 사용하고 이외에도 다른 클래스에서도 산술연산자를 사용해야하는 경우가 있다 <br>
예시로 포인터를 더한다고 봐보자 <br>
```kotlin
package me.kyu9.ktexercise.ch6

data class Point(
    val x: Int,
    val y: Int
    )
{
    operator fun plus(other: Point): Point{
        return Point(this.x + other.x, this.y + other.y)
    }
}
```
테스트 <br>
```kotlin
package me.kyu9.ktexercise

import me.kyu9.ktexercise.ch6.Point
import org.junit.jupiter.api.Assertions
import org.junit.jupiter.api.Test

class PointTest {
    @Test
    fun plusCheck(){
        val p1 = Point(1, 2)
        val p2 = Point(2, 1)

        val p3 = p1 + p2
        println(p3)
        Assertions.assertEquals(p3.x, 3)
        Assertions.assertEquals(p3.y, 3)
    }
}
```
이렇게 사용할 수 있는데 plus을 오버로딩 + 연산자를 통해서 사용을 한것이다 <br>
근데 연산자를 오버로딩해서 사용하기 위해서는 해당 함수 앞에 operator 이라는 키워드를 넣어주어야 한다 <br>
만약에 키워드 안넣고 이름을 사용해서 구현하면 operator modifier is required 에러를 통해서 뭐가 문제인지 바로 알 수 있다 <br>
이러한 산술연산자는 +을 제외하고도 여러가지 있다 <br>
a*b -> times <br>
a/b -> div <br>
a%b -> mod <br>
a+b -> plus <br>
a-b -> minus <br>
이렇게 재정의한 연산자들을 사용할때 연산자의 우선순위는 우리가 기존에 알고 있는 연산자의 우선순위와같다 <br>
그리고 코틀린 연산자는 자동으로 교환법칙을 지원하지 않는다 -> point*2 을 구현하고 2*point 이렇게 계산하면 안된다 새로 다시 구현해주어야 한다 <br>
그리고 += -= 와 같은 복합 대입도 가능하다! <br>
<br>

그리고 단항 연산자도 오버로딩할 수 있다 <br>
방식은 100% 같지만 다른 점으로는 인자를 받지 않는다는 점이다 <br>
+a -> unaryPlus <br>
-a -> unaryMinus <br>
!a -> not <br>
++a, a++ -> inc <br>
--a, a-- -> dec <br>
개념자체도 재정의를 해도 같은 개념으로 흘러가기 떄문에 그렇게 복잡한 점은 없다 <br>
<br><br><br>

### 비교 연산자 오버로딩
산술 연산자와 마찬가지로 원시 타입 값 뿐만 아니라 모든 객체에 대해서 비교연산을 수행하는 것이 가능하다 <br>
자바에서는 equals나 compareTo를 통해서 비교를 했었는데 코틀린에서는 ==을 통해서 비교를 한다 <br>
<br>

코틀린에서는 ==이 equals 메소드를 호출하게 되는 원리이다 추가로 != 이 연산자도 equals 호출로 컴파일이 된다 <br>
물론 그대로 사용되는건 아니고 비교결과의 반대값을 결과 값으로 사용되긴한다 <br>
추가로 ==이나 != 모두 내부에서 null에 대한 검사를 진행하기 때문에 nullable에도 적용된다 <br>
a==b -> a?.equals(b)?: (b == null) <br>
근데 기본적으로 엔티티는 data 클래스로 생성하는데, data 클래스에서는 따로 구현하지 않아도 자동으로 equals을 구현해준다 <br>
만약에 직접 구현하고 싶다면 연산자처럼 뭔 키워드가 있지는 않지만 <br>
```kotlin
override fun equals(other: Any?): Boolean {
        if(other == this) return true
        if(other !is Point) return false
        return other.x==this.x && other.y==this.y
    }
```
이렇게 override으로 자동으로 지원해주는 것에 있는 equals을 상속받아보면 Any 타입의 객체를 받아서 비교를 하게 된다 
Any의 equals에는 operator가 붙어있지만 override 할때는 따로 operator를 붙히지 않아도 자동으로 상위 클래스인 any의 operator 지정이 적용되기 떄문에 
굳이 작성해주지 않는 것이다 <br>
또한 any에서 상속받은 equals의 함수가 만약에 커스텀해서 또 새로운 equals을 넣어두면 우선순위 자체가 기본으로 제공해주는 any의 equals가 높기 떄문에 equals는 확장 함수로 정의할 수 없다 <br>
<br><br>

compareTo도 존재한다 이건 원래 사용할때마다 막 Comparealbe 인터페이스를 통해서 직접 재구현해주고 이랬었다 <br>
Compareable 에 들어있는 compareTo 메소드는 한 객체와 다른 객체의 크기를 비교해서 정수로 나타내주는데, 자바에서는 이걸 원시 값을 바로 사용하는 것이 아니라 직접 a.compareTo(b) 이렇게 사용했었다 <br>
코틀린은 친절하게도 비교연산자가 compareTo 메소드의 호출로 진행된다 <br>
```kotlin
override fun compareTo(other: Point): Int {
        return compareValuesBy(this, other, Point::x, Point::y)
    }
```
compareTo를 재정의하는걸 보면 compareValuesBy 을 사용하면 더욱 간결하게 정의할 수 있다 <br>
compareValuesBy는 두 객체와 여러 비교함수를 인자로 받는데, 첫 번째 비교함수에 두 객체를 넘겨서 두 객체가 같지 않다는 결과가 나오면 
그 결과를 즉시 반환하고 두 객체가 같다는 결과가 나오면 두 번째 비교 함수를 통해 두 객체를 비교한다 <br>
근데 중요한건? 굳이 재정의하지 않고 단순하게 비교연산자를 통해서 compareTo가 호출되기 떄문에 잘 사용하자 <br>
<br><br><br>

### 컬렉션과 범위에 대해 쓸 수 있는 문제 
컬렉션하면 또 많이 쓰는게 원소를 읽고 쓰는것과 컬렉션 내부에 있는지 비교하는 연산을 많이 쓴다 <br>
컬렉션에서 인덱스를 통해서 원소를 설정하거나 가져올 때 a[b] 이렇게 사용하고 내부에 있는지에 대한 검증은 in 키워드를 사용하는데 사용자 지정 클래스로 이런 연산을 추가하는 것이 가능하다 <br>
<br>

인덱스를 통한 원소 접근 - get, set <br>
코틀린에서 인덱스에 접근할 때 []을 통해서 사용하는데, 인덱스로 원소를 읽을 때는 [] 요게 get 함수로 변환되고 원소를 쓸 때는 [] 요게 set 함수로 변환된다 <br>
get을 사용할때도 똑같이 operator을 사용해서 정의해주면 된다 <br>
```kotlin
    operator fun Point.get(index: Int): Int{
        return when(index){
            0 -> this.x
            1 -> this.y
            else -> throw IndexOutOfBoundsException()
        }
    }
```
물론 int 이외에도 다른 타입을 받고 get 을 구현하는 것도 가능하다 <br>
```kotlin
operator fun Point.set(index: Int, value: Int){
        when(index){
            0 -> this.x = value
            1 -> this.y = value
            else -> throw IndexOutOfBoundsException()
        }
    }
```
기존의 point 클래스는 val로 이루어져있어서 set이 필요없어서 var로 변경하고 다시 구현을 해보면 이러한 느낌이다 <br>
<br>

다음은 in인데 in은 객체가 컬렉션 내부에 있는지 검사하는 방식이다 in을 재정의하기 위한 함수는 contain이다 <br>
이것도 동일하게 operator 키워드를 사용해서 해주면 된다 <br>
rangeTo 같은 경우도 from...to -> from.rangeTo(to) 이렇게 컴파일되니 참고해서 사용하면 된다 <br>
<br><br>

### 구조 분해 선언, Component 함수
구조 분해란 복합적인 값을 분해해서 여러 다른 변수를 한꺼번에 초기화하는 방법이다 <br>
```kotlin
val point = Point(10, 20)
val (x, y) = point
//x == 10
//y == 20
```
뭔가 일반 변수 선언과 비슷하지만 = 로 넣을 값을 괄호를 토해서 묶어둔 것이 특징이다 <br>
내부에서 구조 분해 선언의 각 변수를 초기화할때 사용하는 함수는 componentN 이라는 함수이다 <br>
객체선언에서 많이 쓰는 data 클래스 내부에서는 자동으로 컴파일러가 componentN을 만들어준다 <br>
직접 구현할 떄는 <br>
```java
...
operator fun component1() = x
operator fun component2() = y
...
```
이런식으로 나눠서 구현된다 여기서 구조 분해 선언은 함수에서 여러 값을 반환할 때 쓸만하다 여러 값을 한꺼번에 반환해야 하는 함수가 있다면 반환해야 하는 모든 값이 들어갈 
데이터 클래스를 정의하고 함수의 반환 타입을 그 데이터 클래스로 반환해줌으로써 어떤 함수가 리턴하는 값을 나눠서 변수에 넣어주는 것이 가능하다 <br>
<br><br>


### 위임 프로퍼티 