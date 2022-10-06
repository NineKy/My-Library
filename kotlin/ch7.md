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
위임 프로퍼티를 사용하면 값을 뒷받침하는 필드를 단순하게 저장하는 것보다는 로직을 넣는 것?이 가능하다 또한 그 접근자 로직을 맨날 구현할 필요도 없다 <br>
예로 프로퍼티는 위임을 사용해서 자신의 값을 필드가 아니라 디비 테이블이나 맵 등에 저장하는 것이다 <br>
위임이란 객체가 직접 작업을 수행하는 것이 아니라 다른 도움객체가 그 작업을 대신 맡아서 처리하는 것을 말한다 그리고 대신 작업을 해주는 애를 위임객체라고 부른다 <br>
일반적으로 이렇게 작성한다
```kotlin
class Foo {
    var p: Type by Delegate()
}
```
프로퍼티인 p는 접근자 로직을 다른 객체에게 위임하는데 우선 여기선느 Delegate 클래스의 인스턴스를 위임 객체로 사용한다는 의미이고 by 키워드를 통해서 선언한다 <br>
여기서 Delegate() 요건 getValue와 setValue 이 2개를 가지고 있어야 하고 새로 만들어야 하는 클래스이다 <br>
setValue 같은 경우에는 물론 변경 가능한 프로퍼티만 사용한다 그래서 구현은 어떻게 ? <br>
```kotlin
package me.kyu9.ktexercise.ch6

import lombok.experimental.Delegate
import java.lang.reflect.Type
import kotlin.reflect.KProperty

class MyDelegate(var p: String) {
    operator fun getValue(foo: Foo, property: KProperty<*>): String{
        println("This is GETTER -> $p")
        return p
    }

    operator fun setValue(foo: Foo, property: KProperty<*>, str: String){
        println("This is SETTER -> $str")
        p = str
    }
}


class Foo {
    var p: String by MyDelegate("sample")

}

fun main() {
    val foo = Foo()
    val oldV = foo.p
    foo.p = "checkthisout"
}
```
이렇게 해두면 <br>
This is GETTER -> sample <br>
This is SETTER -> checkthisout <br>
이렇게 기존에 구현해두었던 getValue, setValue 을 자동으로 불러오게된다 <br>
foo.p는 일반 프로퍼티처럼 보이지만 사실 MyDelegate 타입의 위임 프로퍼티 객체에 있는 메소드를 호출하는 방식이다 <br>
<br><br>

지연 초기화라는 것은 개념은 객체의 일부분을 미리 초기화하지 않고 남겨두었다가 필요할때 그 시점에 초기화해주는 그러한 개념이다 <br>
책에서 들어준 예시는 이러하다 <br>
```kotlin
class Person(val name: String) {
    //데이터를 저장하고 emails의 위임 객체 역할을 하는 _emails 프로퍼티
    private var _emails: List<String>? = null

    val emails: List<String>
        get(){
            if(_emails == null){
                //여기가 대충 db에서 조회해서 넣어주는 곳
                //최조 접근 시 값을 가져온다
                _emails = search(this)
            }
            //만약에 저장된게 있다면 저장된걸 반환한다
            return _emails!!
        }

    fun search(person: Person): List<String>{
        println("이 함수는 단 한 번 만 불 러 져 야 한 다")
        return mutableListOf("QWER", "ASDF")
    }
}


fun main() {
    val p = Person("Charile")
    //그리고 최초로 email 값을 읽을 때 한 번만 가져오고
    println(p.emails)
    //두 번째로는 그냥 다시 조회해서 오는것이 아니라 초기화된걸 가져오는 방식이다 
    println(p.emails)
}
```
이러한 방식을 보고 backing property(뒷받침하는 프로퍼티)라고 한다 <br>
여기서 _email이라는 서브 프로퍼티에다가 값을 저장해두고 email에서 _email을 get 하는 방식인 것이다 <br>
근데 이건 사실 프로퍼티가 많거나 하면 코드가 많이 복잡해지기도 하고 스레드 safe 한 방식은 아니다 <br>
그래서 사용하는게 위임 프로퍼티라고 한단 -> 위임 프로퍼티는 데이터를 저장할 때 사용되는 프로퍼티와 같이 단 한 번만 초기화 해주는 getter 를 만들어준다 <br>
단순하게 lazy 타입만지정해주면 <br>
```kotlin
class Person(val name: String) {

    val emails by lazy{search(this)}

    fun search(person: Person): List<String>{
        println("이 함수는 단 한 번 만 불 러 져 야 한 다")
        return mutableListOf("QWER", "ASDF")
    }
}
```
이렇게 단순하게 코드가 줄여진다 <br>
lazy 함수는 getValue 메소드가 들어있는 객체를 반환해주기 때문에 by 키워드와 함께 위임 프로퍼티를 만드는 것이다 <br>
lazy 함수의 인자는 값을 초기화할때 호출하는 람다이고, 스레드 safe 하면서도 동기화 컨트롤도 가능하다 <br>
<br><br>

위임 프로퍼티 컴파일 규칙 <br>
처음에 이거 직접 만들고 있는데 뭔가 바로바로 안되서 뭔가 싶었는데, 위의 코드에서 보면 <br>
컴파일러는 Type by 뒤에 넣어준 MyDelegate 이라는 클래스의 인스턴스를 숨겨둔 프로퍼티에 저장해서 그 숨겨진 프로퍼티를 보고 <delegate> 이렇게 표현하고,
이 프로퍼티를 표현하기 위해서 KProperty 이라는 타입의 객체를 사용한다 여기서 KProperty 봤었다..! getValue, setValue 에서 파라미터로 받는 타입이였다... <br>
그래서 getValue, setValue 에서 property: KProperty<*> 요렇게 해두면 컴파일될 때 위의 예시인 MyDelegate 이렇게 들어가진다 <br>
자 그래서 이러한 매커니즘은 어디서 쓰냐 --> 프로퍼티 값이 저장되는 장소를 바꿀 수도 있고(맵, 디비, 쿠키 등), 프로퍼티를 읽거나 사용할 때 벌어지는 일을 변경하는 것이 가능하다 <br>
책에서는 이리저리 예시를 들어주긴하는데... 실제로 디비에 접근해서 값을 처리하는 그러한 방식을 소개해주는 듯 했다 단순하게 엔티티용객체1, 프로퍼티가 객체1과 동일한 위임전용객체2, 이렇게 만들고
위임전용객체에서 엔티티용객체의 프로퍼티를 바라보도록 by 객체1.프로퍼티 이렇게 잡아두고 getValue, setValue을 통해서 위에서 봤던 이점들을 활용하라 이라는 것 같았다 <br>
근데 디비같은경우에는 직접 바라보면서 개발을 하지는 않아서 그렇게 와닿지는 않았다 <br>
아무튼 이렇게 위임 프로퍼티를 통해서 프로퍼티 값을 저장하거나 초기화, 읽, 쓰 하는 로직에서 유용하게 쓸 수 있으며, 프레임워크를 만드는데 유용하다는 결론 <br>
<br><br><br>

<br><br><br><br><br><br><br><br><br><br>

































