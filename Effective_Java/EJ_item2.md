# 생성자에 매개변수가 많다면 빌더를 고려하라

짚고 넘어가는 부분들은 이와같다 <br>
생성자 체이닝과 자바빈즈 <br>
빌더 <br>
계층형 빌더 <br>
<br><br><br>

기본적으로 객체를 생성하기 위해서는 생성자를 배우고 사용한다 <br>
가장 기본적인 방법이기도하며 가장 처음 배우는 방법이다 <br>
하지만 만약 해당 객체의 필드 수가 많아지는 순간이 생성자를 정--말 만들기 싫어지는 그런 순간이다 <br>
예를 들어서 필드가 6개만 되어도 만들 수 있는 생성자가 몇개인지 정말 많아진다 <br>
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
<br>
물론 위의 예시는 조금 많이 극단적이구.. 솔직히 그정도는 아닌데 이렇게 길어지는 것을 대신하는 방법도 있다는 것이다 <br>

<br><br>
## 생성자 체이닝과 자바빈즈
객체를 생성하는 방법들 중에서 간단?하고 가장 잘 알고있는 방법 두 가지이다 <br>

우선 <br>
생성자 체이닝의 의미는 this나 super 키워드를 사용해서 생성자에서 내부 생성자를 호출하는 방식이다. 이렇게만 사용해도 위의 Teeth 객체에서의 중복 코드를 상당하게 줄이는 것이 가능하다 <br>
우선 객체를 new 키워드를 통해서 생성할때 보면, 따로 선언하지 않더라도 기본적으로 제공해주는 아무런 매개변수를 받지 않는 생성자를 통해 생성한다 <br>
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
**단순하게 this.필드 작업의 반복을 단순하게 처리해줄 수 있다는 장점이 있구나!** <br><br>

두 번째로는 <br>
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
요렇게 사용하는 방법인데 진짜 가장 간단하면서도 가장 flexable? 한 느낌의 객체 생성 방법이지만... <br>
자바빈즈의 단점으로는 역시 필드를 잘 모르는 상황에 사용하다보면 몇 개는 빼고 객체를 생성할 수도 있기 때문에 <br>
객체를 생성하는데 위험성을 안고 생성하는 기분이다 <br>
또한 객체를 생성해 놓고 보면 어디서 사용할지도 모르는데, 만약 자바빈즈를 통해서 객체를 생성해버리면 <br>
어떤 객체를 완벽한 모든 필드를 가지고 만들었을 수도 있지만 어떤 객체를 필드가 빠진 상태로 만들어질 수 도 있다 <br>
그것이 이상한 상황이라고 볼 수는 없다 상황에 따라서 다를 수 있기 때문이다 <br>
근데 객체의 일관성을 고려해야하는 객체를 생성해야 하는 일이 있다면 자바빈즈는 피하는게 좋은 방식이라고 생각한다 <br>
<br><br><br>


