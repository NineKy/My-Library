# 기초

## 함수
함수의 특징으로는 몇 가지가 있다 <br>
함수를 선언하는데 있어서 함수의 선언은 fun 이라는 키워드를 통해서 만든다 <br>
파라미터 이름 뒤에 그 파라미터의 타입을 작성한다 <br>
함수를 굳이 클래스 내부에 정의하지 않고 그 외부에서도 함수를 정의할 수 있다 <br>
배열도 자바와 다르게 배열 처리를 위한 문법은 없다 <br>
<br>

statement, expression <br>
statement는 선언문이고, expression은 식이다 <br>
식은 값을 만들어내고 다른 식의 하위 요소로 계산에 참여할 수 있는 특징이 있다 <br>
선언문은 자신을 둘러싸고 있는 가장 안쪽 블록의 최상위 요소로 존재하면서 아무런 값을 만들지 않는다 <br>
<br>

함수를 이렇게 2개로 나눌 수 있다 - 인텔리제이에 변환팁을 준다
```kotlin
//블록이 본문인 함수
fun bigger(x: Int, y: Int): Int{
        return if(x>y) x else y
    }
    
    
//식이 본문인 함수
fun ktBigger(x: Int, y: Int) = if(x>y) x else y
```
식이 본문인 함수같은 경우에는 코틀린 컴파일러가 해당 식에 반환 타입을 추론해서 식의 결과를 함수의 반환타입으로 설정해준다 <br>
그리고 이렇게 컴파일러가 타입을 분석해서 프로그램의 구성요소를 정해주는 것이 타입 추론이라고 한다 <br>
중요한거는 결국 식이 본문인 함수만 return만 생략하는 것이 가능하다 그리고 뭔가 식으로 함수를 구현하면 리턴에 대한 가시적인게 없어서 살짝 아쉽기도 하다 <br>


## 변수
```kotlin
fun value(){
        //이렇게 타입을 따로 정해주지 않고 선언하는 것도 가능하고
        val a = "abc"
        val b = 123

        //타입을 따로 지정해주는 것도 가능하다
        val aa: Int = 24
        val bb: String = "abc"
    }
```
타입 추론을 사용해서 하는 것도 가능하고, 타입을 따로 정하지 않고도 사용할 수 있다 <br>
<br>

코틀린의 변수는 자바스크립트와 유사하다 <br>
val : 변경할 수 없는 참조를 저장하는 변수이다. 즉, val로 선언된 변수는 초기화하고 나서는 다시 값을 못 집어넣는다(java의 final)
var : 변경할 수 있는 참조를 저장하는 변수이다. 자바에서 우리가 자유롭게 사용하는 변수와 같은 것이라고 보면 된다 <br>
<br>

기본적으로 추천 -> 모든 변수를 val 키워드를 사용해 불변 변수로 선언하고, 나중에 필요할때만 var로 변경하자 <br>
val 변수는 블록을 실행할 때 정확히 한 번만 초기화되야 하지만 선언 후에 값을 단 한번 넣은 로직이 있다면 이렇게도 가능한 것으로 보인다 <br>
```kotlin
    fun value2(t: Boolean){
        val test: String
        if(t){
            test = "Success"
        }else{
            test = "False"
        }

        val test2: String
        test2 = if(t){
            "Success"
        }else{
            "False"
        }

        val test3: String = if(t){
            "Success"
        }else{
            "False"
        }
    }
```
단일 변수는 이렇게 설정하는데, 변수에 대한 값은 변경할 수 없어도 해당 변수가 객체일때는 객체의 내부 값은 변경되는 것이 가능하다 <br>
list를 예시로 보면 이러하다 <br>

```kotlin
fun value3(){
        val arrayTest = arrayListOf("Java")
        arrayTest.add("Kotlin")
        arrayTest.forEach(System.out::println)
    }
```

var 타입 같은 경우에는 값을 변경하는 것이 가능하다고 했는데, 처음으로 선언해준 변수의 타입은 고정된다 <br>
즉 Int으로 선언해두고 String 값으로 변환하려고 하는건 안된다는 의미이다 <br>
컴파일러는 변수를 선언하는 시점에 초기화하는 식으로부터 변수의 타입을 추론하고, 변수의 선언 이후에 변수에 다시 대입할때, 이미 추론한 변수의 타입을 염두하고 타입을 검사한다 <br>
타입은 나중에 변환함수라는 걸 사용해서 변환하는게 가능하다고 한다 <br>
<br><br>

코틀린에는 문자열 템플릿이라는 기능이 있다 <br>
자바에서는 문자열에서 String과 변수를 함께 사용하는 경우에는 항상 "스트"+변수+"링" 이런식으로 사용을 해왔는데 <br>
사용하는 방법은 그렇게 어렵지 않게 $변수 이렇게 사용하면 된다 많이 사용해본 방식이니까 그렇게 어색하지는 않다 <br>
단순 변수말고도 좀 더 길게 사용하고 싶다면, ${} 을 통해서 조금 더 긴 방식으로도 사용할 수 있다 <br>
그리고 문자열템플릿을 사용하는데 있어서 한글로 사용할 때 이슈가 조금은 있을 수 있다고한다 -> 그냥 ${}을 통해서도 단순 단어도 사용할 수 있다고하니 ${}으로 사용하자 그냥 <br>
<br><br>

## 클래스와 필드
지금까지 단순 데이터를 처리하는 법을 봤으면 이제는 실제 객체를 만들어보자 <br>
자바와의 차이를 봐보자 <br>
```java
public class Person{
    private final String name;
    private String addr;
    private int age;
}
```
이러한 자바의 클래스와 코틀린을 비교해보자 <br>
```kotlin

```
<br><br>

클래스, 즉 객체는 자신이 가지고 있는 객체들이나 해당 객체를 사용할때 직접 사용하지 않고 항상 접근자 메소드를 사용한다 <br>
그 접근자 메소드의 예시로는 게터나 세터가 있다 -> 그래서 자바에서는 항상 롬복 애노테이션을 통해서 사용해왔는데 코틀린은 기본적으로 접근자 메소드를 제공해준다 <br>
val같은 경우에는 읽기 전용이고, var같은 경우에는 세터도 가능하다 <br>
```kotlin
class Person(val name: String, var addr: String, var age: Int) {
}


fun personTest(){
        var person = Person("testName", "testAddr", 10)
        println(person.addr == "testAddr") //true
        println("his name is ${person.name}") //his name is testAddr
        println(person.age) //10
    }
```
이렇게 사용할 수 있다 따로 getter를 구현하지 않고도 객체.필드명 을 통해서 게터를 사용하는 것이 가능하다 <br>
만약에 기본적으로 제공해주는 것들이 아닌 새로 구현하고 싶다면 자바에서 필드 구현하는 것 처럼 구현해주면 된다 <br>
대신 주의할 점으로는 커스텀해서 만든 변수는 get() 함수를 다시 구현해줘야하는 점을 기억하자 <br>
<br><br>

Enum, When <br>
when 이라는 키워드는 자바에서의 switch 문을 대신하지만 더 좋은 방식이라고 한다 <br>
<br>

일단 Enum 먼저 한번 보자 
```kotlin
enum class Color(val x: Int, val y: Int, val z: Int) {
    RED(1, 1, 1),
    ORANGE(2, 2, 2),
    YELLOW(3, 3, 3),
    GREEN(4, 4, 4),
    BLUE(5, 5, 5),
    NAVY(6, 6, 6),
    PURPLE(7, 7, 7)
    ;

    fun getRgb() = x + y + z
}


fun colorTest(){
        println(Color.BLUE.getRgb()) //15
    }
```
enum의 사용법 자체는 자바에서 사용하는 것과 크게 다르지는 않다. 하지만 특징으로 보면, 코틀린인데도 ;을 사용해서 마무리해주는 것을 볼 수 있다 <br>
이렇게 enum 클래스 안에서는 메소드를 정의할 때 반드시 상수목록이 끝나는 곳에 ;을 통해서 구분을 지어주어야 한다 <br>
<br>
이제는 그럼 enum을 각 조건에 맞게 다른 값을 처리하고 싶을 것이다! 물론 switch문의 대신인 when을 설명하기 위한 것이다.. <br>
when 이라는 키워드도 if와 같이 값을 만들어내는 식이다. 따라서 식이 본문인 함수에 when을 바로 사용하는 것이 가능 <br>
사용하는 방법은 이와 같다 <br>
```kotlin
fun whenTest(color: Color){
        when(color){
            Color.RED -> "I'm Red"
            Color.ORANGE -> "I'm Orange"
            Color.YELLOW -> "I'm Yellow"
            Color.GREEN -> "I'm Green"
            Color.BLUE -> "I'm Blue"
            Color.NAVY -> "I'm Navy"
            Color.PURPLE -> "I'm Purple"
        }
    }
```
참고로 enum은 import을 통해서 빼내고 사용해도 된다 <br>
역시 switch와 비슷하고 같은 역할을 하는 것 같은데, 다른 점으로는 역시 break 키워드가 없다는 점이다 <br>
그리고 추가로 콤마를 사용해서 여러개에 대한 분기를 처리하는 것이 가능하다는 점이 특징이다 <br>
<br>

switch보다 좋은 점으로는 일단 분기조건을 거는데 있어서 임의의 객체를 허용한다 <br>
```kotlin
fun mixTest(c1: Color, c2: Color){
        when(setOf(c1, c2)){
            setOf(Color.RED, Color.YELLOW) -> Color.ORANGE
            setOf(Color.YELLOW, Color.BLUE) -> Color.GREEN
            else -> throw Exception("MIX ERROR!")
        }
    }
```
이런식으로 사용할 수도 있으며, setOf 이라는 타입을 통해서 식을 통해서 비교하는 것이 가능하다 <br>
setOf은 내부의 값이 순서나 그런것들은 중요하지 않고 내부에 무엇이 있는지만이 중요한 그러한 컬랙션을 만드는 메소드이다 <br>
이렇게 setOf 사용한건 equals 통해서 비교한 방식이고, <br>
```kotlin
fun mixTestVER2(c1: Color, c2: Color){
        when{
            c1.x==c2.x && c1.y==c2.y -> "x is same with y"
            c1.x==c2.x && c1.z==c2.z -> "z is same with z"
            else -> throw Exception("no matched")
        }
    }
```
이렇게 switch의 변형문처럼이 아닌, 인자가 없는 방식으로 사용할 수 있다 -> 하지만 인자가 없는 방식은 항상 boolean을 가지고 진행하는 과정에서만 사용할 수 있다 <br>
