# item2 - 생성자에 매개변수가 많다면 빌더를 고려하라

짚고 넘어가는 부분들은 이와같다\
생성자 체이닝과 자바빈즈\
빌더\
계층형 빌더\
\
\
\


기본적으로 객체를 생성하기 위해서는 생성자를 배우고 사용한다\
가장 기본적인 방법이기도하며 가장 처음 배우는 방법이다\
하지만 만약 해당 객체의 필드 수가 많아지는 순간이 생성자를 정--말 만들기 싫어지는 그런 순간이다\
예를 들어서 필드가 6개만 되어도 만들 수 있는 생성자가 몇개인지 정말 많아진다\
~~만약 이렇게 사용해야 한다면 하나의 객체에 몇개가 들어오냐...~~

```java
package me.kdshim.kdd_j.checking;

public class Teeth {
    String first;
    String second;
    String third;
    String fourth;
    String fifth;
    String sixth;

    public Teeth(String first) {
        this.first = first;
    }

    public Teeth(String first, String second) {
        this.first = first;
        this.second = second;
    }

    public Teeth(String first, String second, String third) {
        this.first = first;
        this.second = second;
        this.third = third;
    }

    public Teeth(String first, String second, String third, String fourth) {
        this.first = first;
        this.second = second;
        this.third = third;
        this.fourth = fourth;
    }

    public Teeth(String first, String second, String third, String fourth, String fifth) {
        this.first = first;
        this.second = second;
        this.third = third;
        this.fourth = fourth;
        this.fifth = fifth;
    }

    public Teeth(String first, String second, String third, String fourth, String fifth, String sixth) {
        this.first = first;
        this.second = second;
        this.third = third;
        this.fourth = fourth;
        this.fifth = fifth;
        this.sixth = sixth;
    }
}
```

\
물론 위의 예시는 조금 많이 극단적이구.. 솔직히 그정도는 아닌데 이렇게 길어지는 것을 대신하는 방법도 있다는 것이다\


\
\


## 생성자 체이닝과 자바빈즈

객체를 생성하는 방법들 중에서 간단?하고 가장 잘 알고있는 방법 두 가지이다\


우선\
생성자 체이닝의 의미는 this나 super 키워드를 사용해서 생성자에서 내부 생성자를 호출하는 방식이다. 이렇게만 사용해도 위의 Teeth 객체에서의 중복 코드를 상당하게 줄이는 것이 가능하다\
우선 객체를 new 키워드를 통해서 생성할때 보면, 따로 선언하지 않더라도 기본적으로 제공해주는 아무런 매개변수를 받지 않는 생성자를 통해 생성한다\
생성자 체이닝을 사용하게 되면 아래와 같이 구현될 수 있다

```java
package me.kdshim.kdd_j.checking;

public class Teeth {
    String first;
    String second;
    String third;
    String fourth;
    String fifth;
    String sixth;

    public Teeth() {
    }

    public Teeth(String first) {
        this(first, null, null, null, null, null);
    }

    public Teeth(String first, String second) {
        this(first, second, null, null, null, null);
    }

    public Teeth(String first, String second, String third) {
        this(first, second, third, null, null, null);

    }

    public Teeth(String first, String second, String third, String fourth) {
        this(first, second, third, fourth, null, null);
    }

    public Teeth(String first, String second, String third, String fourth, String fifth) {
        this(first, second, third, fourth, fifth, null);
    }

    public Teeth(String first, String second, String third, String fourth, String fifth, String sixth) {
        this.first = first;
        this.second = second;
        this.third = third;
        this.fourth = fourth;
        this.fifth = fifth;
        this.sixth = sixth;
    }
}
```

**단순하게 this.필드 작업의 반복을 단순하게 처리해줄 수 있다는 장점이 있구나!**\
\


두 번째로는\
자바빈즈 방식인데 말이 자바빈즈라고 하지만 **간단하게 그냥 빈 객체를 만들고 각 필드를 세터를 통해서 객체를 조립하는 방식이다**

```java
public static void main(String[] args) {
        Teeth teeth = new Teeth();
        teeth.setFirst("THIS IS FIRST");
        teeth.setSecond("THIS IS SECOND");
        teeth.setThird("THIS IS THIRD");
        teeth.setFourth("THIS IS FOURTH");
        teeth.setFifth("THIS IS FIFTH");   
    }
```

요렇게 사용하는 방법인데 진짜 가장 간단하면서도 가장 flexable? 한 느낌의 객체 생성 방법이지만...\
자바빈즈의 단점으로는 역시 필드를 잘 모르는 상황에 사용하다보면 몇 개는 빼고 객체를 생성할 수도 있기 때문에\
객체를 생성하는데 위험성을 안고 생성하는 기분이다\
또한 객체를 생성해 놓고 보면 어디서 사용할지도 모르는데, 만약 자바빈즈를 통해서 객체를 생성해버리면\
어떤 객체를 완벽한 모든 필드를 가지고 만들었을 수도 있지만 어떤 객체를 필드가 빠진 상태로 만들어질 수 도 있다\
그것이 이상한 상황이라고 볼 수는 없다 상황에 따라서 다를 수 있기 때문이다\
근데 객체의 일관성을 고려해야하는 객체를 생성해야 하는 일이 있다면 자바빈즈는 피하는게 좋은 방식이라고 생각한다\
\
\
\


그래서 -> 빌더 패턴을 통해서 단점을 보완하는 것이 가능하다\
기존까지의 경우는 생각해보면 새로운 필드가 추가되거나 필드가 아주 많은 경우에는 생성자로 구현해둔 상황에서는 정말 건드려야하는 부분이 많다\
\


장점으로는\
첫 번째로는 원하는 필드로만 객체를 생성하는 것이 가능하다는 점이다\
만약 생성자를 사용한다고 가정하면 원하는 필드로만 이루어진 생성자를 따로 만들어서 적용해야 한다\
이러한 경우는 정말 너무 귀찮기도하다, 필드가 생기면 그 필드를 가진 생성자도 새로 만들어야하기 때문이다\
두 번째로는 가독성이다\
빌더 패턴의 특징으로는 메소드 체이닝을 통해서 객체를 생성한다는 점이다\
메소드 체이닝을 비교하기 좋은 예시는 자바빈즈 패턴을 예시로 볼 수 있다\
필드가 많으면 모든 필드에 대나 각각 setter을 통하거나 모든 필드를 가진 생성자를 통해서만 생성할 수 밖에 없다\
생성자는 그래도 나은 거 같긴 한데 세터는 어떻게 보면 좀 복잡하다고 볼 수 있다\


\
예시를 한 번 보자 이렇게 다양하게 객체를 만들 수 있다\


```java
public static void main(String[] args) {
        Teeth teeth = new Teeth();
        teeth.setFirst("THIS IS FIRST");
        teeth.setSecond("THIS IS SECOND");
        teeth.setThird("THIS IS THIRD");
        teeth.setFourth("THIS IS FOURTH");
        teeth.setFifth("THIS IS FIFTH");
        
        Teeth teeth2 = new Teeth("THIS IS FIRST", "THIS IS SECOND", "THIS IS THIRD", "THIS IS FOURTH", "THIS IS FIFITH");
        
        Teeth teeth3 = Teeth.builder() 
                .first("THIS IS FIRST")
                .second("THIS IS SECOND")
                .third("THIS IS THIRD")
                .fourth("THIS IS FOURTH")
                .fifth("THIS IS FIFTH")
                .build();
    }
```

이렇게만 사용할 수 있는 것도 좋은 방식이다 참고해서 객체 생성하면 좋을 것 같다\
위에 계층형 빌더도 있다 -> 위 예시에서는 애노테이션을 사용해서 빌더를 생성했지만 이외에도 직접 구현하는 방법도 있다\
상속관계에 있는 객체에서 빌더 패턴을 구현한다고 할 때 부모 클래스에서 타입에 구애받지 않게 빌더를 구현한다면 자식클래스에서도 사용할 수 있다는 내용이다\
\


책에서 나온 키워드\
Serialize -> 직렬화, DeSerialize -> 역직렬화 라고 한다\
뭔지 대충은 알고 있지만 정확한 개념으로는 객체를 어딘가에 저장할 때 객체 그대로 저장했다가 가져올 때 그 객체 그대로 가져오는 것을 보장해주는 개념이다\
implementation Serializable을 통해서 적용이 가능하며 이것은 데이터간의 통신이나 디비와의 통신등에 적용되면 좋다\


\
\
checkedException, unCheckedException -> 이 두 예외 타입에 대해서 고민해보자\
우선 RunTimeException을 먼저 봐야한다 여기서 RunTimeException이란, 컴파일러에서 잡아주지 않는, 로직상에서 나오는 에러이다\
checkedException은 RunTimeException을 상속하지 않는 케이스이다 그리고 uncheckedException이 RunTimeException을 상속한 케이스이다\
그럼 checkedException은 에러가 나면 무조건 로직이 깨지기 때문에 try-catch을 통해서 잡아줘야만 하며 그 메소드에 명시를 해줘야 한다\
반대로 unCheckedException은 굳이 명시를 해주지 않아도 괜찮기도 하고 안전하게 에러가 나면 바로 롤백을 통해서 안전하게 처리해준다\
그래서 토이에서도 현재 런타임예외를 상속받는 커스텀 예외를 만들어서 따로 부담 없이 예외를 던도록 처리를 해둔 상황이다\


\
\
가변인수 -> 오.. 난 진짜 처음본다... 자바 1.5부터 나왔던 기능이라고하네요 그리고 printf 와같은 메소드에서 사용한다는 것\


가변인수는 파라미터를 받는데 있어서 매개변수의 숫자를 동적으로 지정해주는 방식을 의미한다\
이렇게 되면 각 파라미터에 대한 생성자가 있다는 가정 하에, 원하는 매개변수 숫자만큼 넣어서 객체를 생성하는 것이 가능하다\
그럼 맨 처음에 나왔었던 다양한 생성자에 대한 복잡함을 그나마 줄여서 사용할 수 있다는 것이 장점인 것 같다
