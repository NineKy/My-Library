# 코틀린 타입 시스템
<br>

### Null Safety
nullability 은 NullPointerException 어레러르 피하게 해주는 간단한 코틀린 타입 시스템의 특성이다 <br>
요 Exception 은 자바에서는 정말 예상하지 못하는 곳에서 넘어와서 해당 로직을 삑나게 하는 아주 나쁜놈이다 
물론 이걸 방지하기 위해서 모든 가능성을 생각하면서 null 조건 체크를 해주는 것도 방법인데.. 이게 정말 말처럼 쉽지가 않다
에이 설마 여기서도 나겠어? 하던게 실제로 그렇게 나와버리는.. 그런 상황이 나온다 <br>
그래서 코틀린에서는 이러한 문제를 찾아내는 시간을 런타임에 체크하는 것이 아니라 컴파일 시점에 체크를 해준다 널이 될 수 있는지의
여부를 타입 시스템에 추가함으로써 컴파일러가 미리 감지해서 미연에 방지가 되도록 만들었다 <br>
<br><br>

널이 될 수 있는 타입은 코틀린에서 명시적으로 지원하고 있다 물론 nullable 하게 만드는 것도 가능하다 <br>
nullable 하게 만드는 방법은 타입을 선언하면서 그 뒤에 ?을 붙히는 것이다 <br>
그럼 기존에 사용하던 방법으로 생각해보면 ?을 붙히지 않은 기본적인 타입은 모두 nullable 하지 않고 컴파일러가 null이 들어갈 수 있다면 빨간줄로 경고한다 
그럼 nullable한 코드는 어떻게 null을 처리할까? -> 이것에 대한 대답은 컴파일러에 있다 <br>
기존에 자바에서 하는 것과 같이 null과 비교하는 과정, 비교하려는것 != null 을 통해서 검사를 해줘면 컴파일러가 아 비교하려는 객체는 null 을 비교하는 부분을 
확인하고 null이 아님을 확실하는 영역에서 작성되도록 도와준다 <br>
이외에도 코틀린에서는 널이 될 수 있는 값을 안전하게 다루도록 도와주는 특별한 연산자들이 많다 <br>
<br><br>

안전한 호출 연산자 ?. 이 있다 <br>
?. 은 null 검사와 메소드호출을 한 번의 연산으로 수행한다 <br>
즉 s?.toUpperCase() 이렇게 작성하면 if(s!=null) s.toUpperCase() else null 이 것과 같은 의미이다 <br>
만약 호출하려는 값이 null 이면 해당 호출이 무시되고 null 값이 되지만 <br>
만약 호출하려는 값이 null 이 아니면 메소드 호출처럼 작동한다는 것이다 <br>
그리고 이것의 포인트는 연쇄해서 사용하는 것도 가능하다는 의미이다 <br>
예를 들어서 메뉴 밑에 메뉴옵션 밑에 메뉴옵션아이템이 있을 때 메뉴 옵션아이템에 접근하고 싶다 <br>
그럼 this.menu?.menuOption?.menuOptionItem 이렇게 접근하면 menu, menuOption에 대한 null 추가 검사 없이 menuOption을 바로 가져오는 것이 가능하다 
이런 식으로 간결하게 null 체크를 할 수 있다는 점이 정말 좋은 것 같다 불필요한 if문 같은 것들도 줄일 수 있는 것이다 <br>
<br><br>

엘비스 연산자라고 불리우는 ?: 이 있다 <br>
null 인경우에 null 대신 default 값을 명시해주는 연산자이다 <br>
val s: String?: "ddd" <br>
이건 만약에 s 변수에 정상적으로 String 값이 들어왔으면 그 값을 적용시키고 <br>
s 변수에 null 이 들어왔으면 "ddd" 가 나온다는 것이다 <br>
아까 위에서 메뉴.메뉴옵션.메뉴옵션아이템 요 구조에서 메뉴옵션아이템마저도 null 이면 안되니까 null인 케이스를 고려해줘야 했다면 <br>
이걸 사용하면 fun getMenuOptionItem() = menu?.menuOption?.menuOptionItem?: "no val" <br>
이렇게 안전한 메뉴옵션아이템의 게터함수를 만드는 것이 가능하다 <br>
이외에 코틀린에서 return 이나 throw 같은 연산도 식으로 치기 때문에 엘비스 연산자 우항에 return 이나 throw 와 같은 연산도 넣을 수 있다 <br>
이러한 패턴은 함수의 전제조건을 검사할때 유용하다 <br>
```kotlin
class Menu(val menuId: String, val menuVersion: MenuVersion)
calss MenuOption(val menuOptionId: String, val menuId: String, val menuOptionVersion)
class MenuVersion(val version: String, val yyyymmdd: String)

fun printMenuVersion(menuOption: MenuOption){
    val getMenuVersion = menuOption.menuId?.menuVersion?: throw IllegalArgumentException("No Version")
        with(getMenuVersion){
            println(yyyymmdd)
            println(version)
        }
}
```
이런 식으로 사용할 수 있다 <br>
<br><br>

타입 캐스팅이 존재하고 as? 이렇게 사용한다 <br>
자바의 타입 캐스트처럼 as로 타입을 변경하는 것이 가능하다 근데 만약에 타입캐스팅에 문제가 있으면 당연하게 타입캐스팅예외가 나올 것이다 <br>
그러면 is을 통해서 타입 검사를 먼저 진행하고 as 으로 변환해야하는데 좀 귀찮은 방식이다 <br>
그런데 as? 이렇게 하면 알아서 처리해주는 것 만약에 타입 캐스팅이 안되면 예외를 던지는게 아니라 null 을 던지게 되어있다 <br>
이렇게 안전하게 캐스트를 수행하고 엘비스 연산자를 자주 사용하는 패턴이 자주 보인다 <br>
<br><br>

널 아님 단언 이라는데.. 그냥 !! 이 느낌표 2개를 의미하는 것 같다 <br>
!! 은 코틀린에서 널이 될 수 있다는 타입의 값을 다룰때 사용한다 
이걸 붙히면 어떤 값이든 널이 될 수 없는 타입으로 강제로 변환해준다 그래서 만약에 실제 null에 대해서 !!을 적용해주면 
널에러가 발생한다 <br>
```kotlin
fun ignoreNulls(s: String?){
    //s 같은경우는 nullable인데 null이 들어갈 수 없는 변수에 넣었기 때문에 발생?
    val sNotNull: String = s!!
    println(sNotNull.length)
}
```
!!은 컴파일러에게 null 값이 들어갈 수 없는 변수이지만 null이 들어가도록 하겠다 라는 의미이다 <br>
문제가 발생할 수도 있다는 것을 의미하기 떄문에 약간 눈에 잘 보이도록 일부러 이렇게 만들었다고 한다 <br>
근데 좋은 방법일때도 있는게 컴파일러 기준으로 함수 내부의 값이 null이 아님을 체크했더라도 안전하다고 판단하지 못하는 경우가 있다 
그래서 호출된 함수가 언제나 널이 아님이 확실하다면 사용하기 딱이다 <br>
그리고 참고?로 !! 이걸 한 라인에서 연속해서 사용하면 어디서 문제가 났는지 파악이 힘들다 그냥 어디줄에서만 에러났다고 나와서
파악하기 힘들기 때문에 연속으로 사용하는건 좋지 않다는 점 <br>
<br><br>


