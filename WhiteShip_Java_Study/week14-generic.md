# Week14\(Generic\)

## 제네릭의 기본 개념

제네릭은 모든 종류의 타입을 다룰 수 있도록, 클래스나 메소드의 매개변수\(generic type\)을 이용하여 선언하는 기법이다. 그리고 본 떠 찍어내기 위해 만들어진 틀이라고 생각하면 편하다. 자바에서의 제네릭은 클래스 코드를 찍어내듯이 생산할 수 있도록 일반화\(generic\)시키는 도구이다.

### +제네릭 타입 매개변수

여기서 타입 매개 변수로 사용하는 문자는 다른 변수와 혼동을 피하기 위해 일반적으로 하나의 대문자를 사용

* E : Element을 의미하여 컬렉션에서 요소임을 나타냄
* T : Type을 의미
* V : Value을 의미
* K : Key을 의미

## 제네릭 컬렉션 생성

벡터 생성할 때, **Vector** 의 E에 요소를 사용할 타입을 지정해야 한다. 예를 들어, 정수 값만 삽입 가능한 벡터를 만들고자 하면 다음 과 같이 E에 Integer를 지정하여 사용가능

```java
Vector<Integer> v = new Vector<Integer>();
```

이렇게 데이터의 타입을 넣어줘야하는데

int, char, double 등의 기본 타입은 E에 사용할 수 없다.

```java
Vector<int> v = new Vector<int>();
```

-&gt;이렇게 하면 에러가 나게 된다.

```java
Vector<Integer> v = new Vector<Integer>(5);
```

이렇게 초기 용량을 지정할 수 있지만 개발자는 생성된 벡터의 용량을 굳이 알 필요 없다. 컬렉션은 자신의 용량을 스스로 조절하기 때문이다. 이게 또 제네릭의 장점이다

### Vector E 클래스의 주요된 메소드

* boolean add\(E element\) 
  * 벡터의 맨 뒤에 element 추가
* void add\(int index, E element\)
  * 인덱스 index에 element를 삽입
* int capacity\(\)
  * 현재 벡터의 용량을 리턴
* void clear\(\)
  * 벡터의 모든 요소 삭제
* boolean contains\(Object o\)
  * 벡터가 지정된 객체를 o이 포함하고 있다면 true를 리턴
* E elementAt\(int index\)
  * index 위치에서의 요소 리턴
* E get\(int index\)
  * index 위치에서의 요소 리턴
* E remove\(int index\)
  * index 위치에서의 요소 삭제
* int size\(\)
  * 벡터가 포함되는 요소의 갯수 리턴

### 벡터의 메소드를 사용하는 예제

벡터에서의 삽입은 벡터의 끝이나 중간에 요소를 삽입할 수 있음

```java
import java.util.Vector;

public class vTest {
    public static void main(String[] args) {
        Vector<Integer> v = new Vector<>();

        //원래는 정수를 Integer 객체를 만들어서 삽입
        v.add(new Integer(1));
        v.add(new Integer(2));

        //자동 박싱 기능을 활용하면 굳이 Integer 객체로 변환되어 삽입될 수 있음
        v.add(3);
        v.add(4);

        //만약 여기에서 다른 객체 타입을 넣을려고 시도하면 에러가 생긴다
//        v.add("hello");
//        v.add(new Double(3,5));

        //벡터에는 null도 삽입할 수 있기 때문에 벡터를 검색할 때 null이 존재할 수도 있음
        v.add(null);

        //원하는 위치에 벡터를 입력하기 위해서는 2개의 인자를 가진 함수사용
        //이렇게 되면 2번째 위치에 100이 들어가게 된다.
        v.add(2, 100);
        //1, 2, 100, 3, 4 여기까지 해두어야함

        //인덱스에 있는 요소를 삭제할 수 있다.
        //벡터에서의 3번째 값이 없어져야하니까 3이 없어져야 한다
        v.remove(3);

        for(int i=0; i<v.size(); i++){
            System.out.println(v.get(i));
        }
    }
}
```

![](../.gitbook/assets/vector.png)

## 제네릭 만들기

### 제네릭 클래스

기존의 클래스를 만드는 방법과 유사하지만 차이점이 있다면 클래스 이름 다음에 일반화된 타입의 매개변수를 &lt;&gt; 사이에 추가하는 것에 차이가 있다.

```java
import java.util.Vector;

public class vTest {
    //제네릭 클래스이며 타입 매개변수는 T
    static class MyClass<T>{
        //변수 val의 타입은 T
        T val;
        void set(T a){
            val = a;
        }
        T get(){
            return val;
        }
    }
    public static void main(String[] args) {
        //T자리에 구체적인 타입을 넣어주고 사용할 수 있다.
        MyClass<String> s = new MyClass<String>();

        //이렇게 값을 넣어주고 출력할 수 있다.
        s.set("HELLO!");
        System.out.println(s.get());

        //구체화할 때는 기본타입사용 불가능(바로 밑 주석)
        //MyClass<int> i = new MyClass<int>();
        MyClass<Integer> i = new MyClass<Integer>();
        i.set(5);
        System.out.println(i.get());
    }
}
```

### 제네릭 메소드

하나의 메소드 선언으로 만들어지며 다른 타입들을 인자로 받아서 호출 될 수 있는 메소드이다

예를 들어서 배열을 리스트로 변환하는 제네릭 메소드를 정의하는 예제

```java
public <T> List<T> fromArrayToList(T[] a){
  return Arrays.stream(a).collect(Collectors.toList());
}
```

여기서 보면 메소드는 하나이상의 제네릭 타입을 처리할 수 있고, 모든 제네릭 타입들이 메소드의 리턴 타입 전 위치에 추가되어야한다.

### Bounded Type Parameter

타입 파라미터들은 bound될 수 있는데, bound된다는 의미는 제한된다는 의미이고, 쉽게 생각하면 **메소드가 받을 수 있는 타입을 제한할 수 있다는 뜻**

```java
public class Box<T> {
    private T t;
    public void set(T t){
        this.t =t;
    }
    public T get() {
        return t;
    }

    public <U extends Number> void inspect(U u){
        //여기서 타입 파라미터 U을 Number로 제한했음 -> extends 키워드를 사용
        //이렇게 되면 U에는 Integer나, Float, Double와 같은 클래스들만 사용가능
        System.out.println("T: "+t.getClass().getName());
        System.out.println("U: "+u.getClass().getName());
    }

    public static void main(String[] args) {
        Box<Integer> ibox = new Box<Integer>();
        ibox.set(10);
        //그렇기 때문에 여기 밑의 주석부분은 에러가 날수밖에 없다
        //ibox.inspect("some text");
    }
}
```

### WildCard

자바에서 와일드 카드는 "?"으로 표시하며 알 수 없는 타입을 의미할 떄 사용된다. 와일드 카드는 다양한 상황에서 사용될 수 있다. 매개변수의 타입, 필드 그리고 지역변수로 사용될수 있다.

Object는 모든 자바 클래스들의 부모 타입이긴 하지만 Object 컬렉션이 다른 컬렉션들의 부모는 아니다.

LIst 타입의 변수에 List변수를 할당하게 되면 컴파일 오류가 발생되는 것을 보고 확인할 수 있다.

이렇게 하기 떄문에 같은 컬렉션에서 서로 다른 타입들을 추가할 경우 발생할 수 있는 오류를 막기 위한 것이다.

Upper Bounded Wildcards

이것을 사용하면 변수에 대한 제한을 줄여줄 수 있다.

사용하는 방법은 "?" 을 extends 키워드 앞에 붙여주면 된다.

```java
public static double sumOfList(List<? extends Number> list) {
    double s = 0.0;
    for (Number n : list)
        s += n.doubleValue();
    return s;
}
```

```java
List<Integer> li = Arrays.asList(1, 2, 3);
System.out.println("sum = " + sumOfList(li));
```

```java
List<Double> ld = Arrays.asList(1.2, 2.3, 3.5);
System.out.println("sum = " + sumOfList(ld));
```

여기서 sumOfList 함수는 Number의 sub type까지 다 사용될 수 있게 된다.

Unbounded Wildcards

printList라는 메소드를 생각해보자, printList함수의 목적은 어느 타입이든지 출력하기 위함이지만 불가능하다. 왜냐하면 이것은 오직 Object의 list만 출력하기 떄문이다. 즉, List, List등 이런 것들을은 List의 서브타입이 아니기 떄문이다.

```java
public static void printList(List<Object> list) {
    for (Object elem : list)
        System.out.println(elem + " ");
    System.out.println();
}
```

그래서 이 함수를

```java
public static void printList(List<?> list) {
    for (Object elem: list)
        System.out.print(elem + " ");
    System.out.println();
}
```

이렇게 와일드카드인 "?" 을 사용해줘서 변경하게 되면

```java
List<Integer> li = Arrays.asList(1, 2, 3);
List<String>  ls = Arrays.asList("one", "two", "three");
printList(li);
printList(ls);
```

이렇게 사용하는데에 문제가 없다!

Lower Bounded Wildcards

원래 upper bounded wildcard가 모르는 타입을 자세한 타입이나 서브타입으로 변환되지 않도록 제한했으며, extends 키워드 앞에서 사용했다면 lower bounded wildcard는 해당 타입의 super 타입까지 허용해주며, super 키워드 앞에 와일드카드인 "?" 을 붙혀서 사용한다.

```java
public static void addNumbers(List<? super Integer> list) {
    for (int i = 1; i <= 10; i++) {
        list.add(i);
    }
}
```

또 다른 예시를 들어서 Integer의 lists에 대한 메소드가 필요하게되면

List&lt;? super Integer&gt; 이렇게 작성함으로써

Integer의 super타입인 Integer, Number, Ojbect이렇게 사용할 수 있다.

그냥 List 이렇게 쓰는것보다 범용성이 넓어진다고 생각한다.

## Erasure Type

제네릭은 자바 언어를 사용함에 있어서 컴파일 시, 더 빡빡한 타입 체크를 가능하게 해주며, 제네릭 프로그래밍을 도와준다. 컴파일러는 컴파일 시, 제네릭에 대해서 type erasure라는 프로세스를 적용한다

* 모든 파라미터들을 그들의 범주안에서 제네릭 타입으로 변경한다.
* 타입의 안전성을 유지하기 위해 필요한 경우, 타입 캐스트를 진행

예시를 보면서 확인

```java
public <T> List<T> genericMethod(List<T> list) { 
  return list.stream().collect(Collectors.toList()); 
}
```

이렇게 진행을 하려고하면

```java
public <T> List<T> genericMethod(List<Object> list) { 
  return list.stream().collect(Collectors.toList()); 
}
```

타입 이레이저가 적용되면서 특정 타입으로 제한되지 않은 T를 위와 같이 Object로 대체된다

만약

```java
public <T extends Building> void genericMethod(T t){
    ...
}
```

이렇게 타입이 제한되어있을 경우에는 이 타입은 컴파일 시점에서 제한된 타입으로 교체

```java
public void genericMethod(Building t){
    ...
}
```

이렇게 제네릭에 관해서 알아보았고, 제네릭은 타입의 정확성을 정확하게 해주며, 애플리케이션으로 하여금 추가적인 오버헤드 없이 제네릭 알고리즘의 구현을 가능하도록 도와주는거 같다

## 참고사이트

명품 자바 프로그래밍 - 황기태

[https://sthwin.tistory.com/22](https://sthwin.tistory.com/22)

[https://medium.com/슬기로운-개발생활/java-generic-자바-제네릭-f4343fa222df](https://medium.com/슬기로운-개발생활/java-generic-자바-제네릭-f4343fa222df)

[https://docs.oracle.com/javase/tutorial/java/generics/wildcards.html](https://docs.oracle.com/javase/tutorial/java/generics/wildcards.html)

[https://docs.oracle.com/javase/tutorial/java/generics/erasure.html](https://docs.oracle.com/javase/tutorial/java/generics/erasure.html)

