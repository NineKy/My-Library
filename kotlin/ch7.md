# 연산자 오버로딩과 기타 관례
<br><br>

코틀린에서 어떤 언어 기능이 정해진 사용자 작성 함수와 연결하는 경우가 몇 가지 있다 <br>
근데 이런 언어 기능이 특정 타입과 연관되는 것보다는 특정 함수 이름과 연관되어 있다 예시로 어떤 클래스 안에 plus 
이라는 이름ㅇ의 메소드를 정의하면 그 클래스의 인스턴스에 대해서 + 연산자를 계산해주는 그러한 기능이다 <br>
이렇게 특정 언어에 미리 정해진 이름의 함수를 연결해주는 기법을 관례기법이라고 한다 <br>
이건 기존 자바 클래스에 대해 확장 함수를 구현하면서 관례에 따라 이름을 붙히면 기존의 자바 코드를 바꾸지 않아도 새로운 기능을 구여할 수 있는 그런 기능이다 <br>
<br><br>


#### 산술 연산자 오버로딩
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

#### 비교 연산자 오버로딩