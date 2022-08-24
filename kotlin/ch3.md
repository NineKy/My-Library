# 함수 정의와 호출
### 코틀린에서 컬렉션 만들기
코틀린에서는 of 이라는 키워드를 사용해서 컬렉션을 만드는 것 같다 전에 setOf 키워드도 사용해서 만드는 방법을 봤었다 <br>
이것 말고도 hashSetOf, arrayListOf 뭐 이렇게 리스트나 맵을 만들 수 있다 <br>
코틀린에서는 컬렉션을 코틀린 전용으로 만든 것이 아니라 자바에 있는 것 그대로 가져왔다 <br>
실제로 자바 컬렉션을 가져와서 사용하기 떄문에 .javaClass (자바에선 getClass())을 통해서 값을 출력해보면 java.util. ~~ 이렇게 나온다 <br>
이렇게 겹치게 사용해서 자바에서 코틀린으로 코틀린에서 자바로 서로 공유하는 것이 가능하다는 것이다 <br>
<br><br>

### 함수를 호출하기 쉽게 만들기
자바에는 여러가지 유틸 라이브러리들을 사용하기 위해서 구아바나 아파치 커먼즈와 같은 라이브러리를 받아서 사용하곤 하는데, 코틀린에는 생각보다 다양한 유틸 라이브러리들을 기본적으로 제공해준다 <br>
직접 유틸성 라이브러리를 만들어보자 <br>
joinToString 이라는 함수가 있다 -> 리스트를 넣으면 toString 처럼 원하는 구분자와 시작과 끝에 원하는 문자를 넣어서 리스트로 쭈루룩 출력할 수 있는 그러한 함수이다 <br>
실제로 구현하면 이러하다고 한다 <br>
```kotlin
fun<T> joinToString(
        collection: Collection<T>,
        seperator: String,
        prefix: String,
        postfix: String
    ): String{
        val result = StringBuilder(prefix)

        for((index, element) in collection.withIndex()){
            if(index > 0)
                result.append(seperator)
            result.append(element)
        }

        result.append(postfix)
        return result.toString();
    }
```
이렇게 구현하는 방식인데, 이렇게 사용하는 걸 보면 joinToString("", "", "") 이렇게 조금은 가독성이 떨어지는 형태가 나와버린다 <br>
그래서 코틀린에서는 조금은 가독성을 챙겨주는 방식이 있다 -> 원래 ide에서는 해당 함수의 파라미터를 슬쩍슬쩍 보여줘서 챙길 수 있도록 도와주는데, <br>
코틀린에서는 직접 파라미터의 이름을 적어도 된다! joinToString(collection, separator="", prefix="", postfix="") 이렇게 쓰는데 명시할꺼면 다 명시해야하는 점을 주의하자 <br>
<br>

그리고 파라미터에 default 값을 넣어주고 싶으면 그냥 엔티티를 선언하는거 처럼
```kotlin
fun<T> joinToString(
        collection: Collection<T>,
        seperator: String="",
        prefix: String="",
        postfix: String=""
    ): String{
        val result = StringBuilder(prefix)

        for((index, element) in collection.withIndex()){
            if(index > 0)
                result.append(seperator)
            result.append(element)
        }

        result.append(postfix)
        return result.toString();
    }
```
default 값을 선언해주면 된다 그리고 특징으로는 만약 default로 잡아두었다면 해당 파라미터를 작성하지 않고도 해당 함수를 사용할 수 있는 것이 특징이다 <br>
joinToString(list) 이렇게 사용하면 위에서 임시 default으로 잡아둔 ""을 기준으로, 맨 앞과 맨뒤에 ""을 넣은 값이 출력된다 <br>
