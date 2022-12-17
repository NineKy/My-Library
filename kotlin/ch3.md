# 함수 정의와 호출

### 코틀린에서 컬렉션 만들기

코틀린에서는 of 이라는 키워드를 사용해서 컬렉션을 만드는 것 같다 전에 setOf 키워드도 사용해서 만드는 방법을 봤었다\
이것 말고도 hashSetOf, arrayListOf 뭐 이렇게 리스트나 맵을 만들 수 있다\
코틀린에서는 컬렉션을 코틀린 전용으로 만든 것이 아니라 자바에 있는 것 그대로 가져왔다\
실제로 자바 컬렉션을 가져와서 사용하기 떄문에 .javaClass (자바에선 getClass())을 통해서 값을 출력해보면 java.util. \~\~ 이렇게 나온다\
이렇게 겹치게 사용해서 자바에서 코틀린으로 코틀린에서 자바로 서로 공유하는 것이 가능하다는 것이다\
\
\


### 함수를 호출하기 쉽게 만들기

자바에는 여러가지 유틸 라이브러리들을 사용하기 위해서 구아바나 아파치 커먼즈와 같은 라이브러리를 받아서 사용하곤 하는데, 코틀린에는 생각보다 다양한 유틸 라이브러리들을 기본적으로 제공해준다\
직접 유틸성 라이브러리를 만들어보자\
joinToString 이라는 함수가 있다 -> 리스트를 넣으면 toString 처럼 원하는 구분자와 시작과 끝에 원하는 문자를 넣어서 리스트로 쭈루룩 출력할 수 있는 그러한 함수이다\
실제로 구현하면 이러하다고 한다\


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

이렇게 구현하는 방식인데, 이렇게 사용하는 걸 보면 joinToString("", "", "") 이렇게 조금은 가독성이 떨어지는 형태가 나와버린다\
그래서 코틀린에서는 조금은 가독성을 챙겨주는 방식이 있다 -> 원래 ide에서는 해당 함수의 파라미터를 슬쩍슬쩍 보여줘서 챙길 수 있도록 도와주는데,\
코틀린에서는 직접 파라미터의 이름을 적어도 된다! joinToString(collection, separator="", prefix="", postfix="") 이렇게 쓰는데 명시할꺼면 다 명시해야하는 점을 주의하자\
\


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

default 값을 선언해주면 된다 그리고 특징으로는 만약 default로 잡아두었다면 해당 파라미터를 작성하지 않고도 해당 함수를 사용할 수 있는 것이 특징이다\
joinToString(list) 이렇게 사용하면 위에서 임시 default으로 잡아둔 ""을 기준으로, 맨 앞과 맨뒤에 ""을 넣은 값이 출력된다\
\
\
\


### 확장 함수와 확장 프로퍼티

확장 함수란 클래스 내부에 존재하는 메소드처럼 사용할 수 있지만 사실 그 클래스 밖에 선언되어 있는 함수이다\
확장함수의 예시로 이런게 있다\


```kotlin
fun String.lastChar(): Char = this.get(this.length-1)
```

확장 함수를 만들기 위해서는 추가하려는 함수 이름 앞에 그 함수가 확장할 클래스의 이름을 붙히기만 한다\
클래스 이름에는 수신 객체 타입이라고 부르고 String.lastChar 요걸 의미한다\
그리고 확장함수가 호출되는 대상이 되는 값을보고 수신객체라고 부르고 this 에 해당한다\
즉, 수신 객체 타입은 확장이 정의될 클래스의 타입이며, 수신 객체는 그 클래스에 속한 인스턴스 객체이다\
확장 함수 내부에서는 일반적인 인스턴스 메소드의 내부에서와 마찬가지로 바로바로 수신 객체의 메소드나 프로퍼티를 사용하는 것이 가능하다 그래서 호출하는 쪽에서는 확장 함수와 멤버 메소드를 구분할 수 없다\
\
\


임포트와 확장함수\
확장함수를 만들어서 사용할 떄 문제는 똑같은 이름이 있으면 충돌이 일어나서 번거롭다.. 그래서 임포트할 때 풀패키지명을 적어주고 맨 끝에 as \~\~ 이렇게 별칭을 사용해서 충돌을 해결해서 사용하자\
\
\


확장함수는 override 할 수 없다 -> 코틀린은 호출될 확장함수를 정적으로 결정하기 때문에 할 수 없다\
추가로 인자를 숫자 프리하게 넣는 방법도 존재한다. 그리고 그걸 스프레드 연산자라고 하고 \*args 이렇게 사용한다\
\
\
\


## 문자열과 정규식 다루기

코틀린에서 사용하는 문자열은 자바에서 사용하는 것과 동일하다 하지만 장점으로는 역시 코틀린 답게 다양한 확장 함수를 제공함으로써 문자열을 더욱 다양하게 사용할 수 있도록 도와준다\
\


문자열 나누기\
split 함수라는 문자열을 배열로 찢어주는 함수에 이게 구분자를 넣어서 어떠한 구분자를 기준으로 나눈다\
근데 문자열을 나누는 과정에서 구분자에다가 . 을 넣으면 에러가 난다 -> 그 이유는 split 내부적으로는 정규표현식을 통해서 구분되는데 . 은 정규표현식에서 모든 문자를 의미하기 때문이다\
이런 면에서는 코틀린에서는 다양한 함수를 제공해주기 때문에 단 하나의 문자를 받는 자바와는 다르게 원하는대로 다양한 문자로도 사용할 수 있다\
\


여러 줄 3중 따옴표 문자열\
3중 따옴표 문자열에는 아무런 이스케이프가 적용되지 않는다 모든 문자열이 문자 그대로 표현이된다\
상당히 긴 문자열을 표현하고 싶다면 사용하기 딱 좋다고 볼 수 있다 그래서 변수 같은 것을 사용하고 싶다면 조금은 귀찮다.. 이렇게 다양한 함수들이 존재하니 필요할 때 찾아서 사용하자\
\
\
\


## 로컬 함수와 확장

코틀린에서는 함수에서 추출한 함수를 원함수 내부에 중첩시키는 것이 가능하다\
간단한 인자 검증 후 저장하는 함수에서 중복을 줄여가며 어떻게 하는지 보자\
처음으로 만든 함수는 이러하다

```kotlin
class User(val id: Int, val name: String, val address: String)

    fun saveUser(user: User){
        if(user.name.isEmpty()){
            throw IllegalArgumentException("[${user.id}] : name is Empty!")
        }

        if(user.address.isEmpty()){
            throw IllegalArgumentException("[${user.id}] : address is Empty!")
        }

        //userRepository.save(user);
    }   
```

여기서 보면 모든 사용자 필드를 각각 검사를 진행하는데 어떻게 보면 중복이라고도 볼 수 있는 것 같다\
\


이럴 때 검증 코드를 로컬 함수로 분리하면 중복을 없애는 동시에 코드 구조를 깔끔하게 할 수 있다\
그래서 이렇게 수정할 수 있다\


```kotlin
fun saveUserVer2(user: User){
        fun validate(user: User, value: String, fieldName: String){
            if(value.isEmpty())
                throw IllegalArgumentException("[${user.id}] : $fieldName is Empty!")
        }
        
        validate(user, user.name, "name")
        validate(user, user.address, "address")
        
        //userRepository.save(user)
    }
```

요렇게 어떻게 보면 그냥 함수를 만들어서 빼는 방식이다. 근데 이렇게하면 검증 로직중복은 사라졌고 다른 필드에 대한 검증추가도 쉽게 할 수 있다\
그래도 여전히 남아있는 불편한 점은 결국은 로컬 함수에 각각의 필드를 넣어서 만들어야 한다는 점? 이다\
그래서 검증 로직을 User 클래스를 확장한 함수로 만드는 것이 가능하다\


```kotlin
fun User.validateBeforeSave(){
        fun validate(value: String, fieldName: String){
            if(value.isEmpty()){
                throw IllegalArgumentException("[${this.id}] : $fieldName is Empty!")
            }
        }
        
        validate(name, "name")
        validate(address, "address")
    }
    
    fun saveUser3(user: User){
        user.validateBeforeSave()
        
        //userRepository.save(user)
    }
```

이렇게 코드를 확장 함수로 뽑아내는 기법은 아주 유용하다 이렇게 확장 함수를 로컬 함수로 정의할 수 있다. User.validateBeforeSave를 saveUser 내부에 로컬 함수로 넣을 수 있다\
근데 한 단계 이상으로 함수를 중첩시키면 많이 복잡하기 때문에 일반적으로는 한 단계만 함수를 중첩해서 사용하곤 한다\
\
\
\


\
\
\
\
\
\
\
\
\
\
