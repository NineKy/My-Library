# Week8\(인터페이스\)

* 서로 다른 하드웨어 장치들이 데이터를 주고 받을 수 있는 규격을 의미
* 인터페이스는 하위 클래스에게 인터페이스의 메소드가 반드시 존재하도록 강제한다
* 일종의 양식을 제공하고 해당 양식에 따라서 자유롭게 코드를 작성할 수 있음 =&gt; 다형성의 특징을 잘 활용

## 인터페이스 정의하는 방법

interface 키워드를 사용해서 클래스를 선언하듯이 선언한다

```java
interface PhoneInterface{
    public static final int BUTTONS = 20;
    abstract public void sendCall();
    abstract public void receiveCall();
}
```

특징

* 인터페이스의 멤버는 누구나 접근할 수 있도록 public 타입이다
* 지정하든 생략하든 상수는 모두 public static final 타입\(생략가능\)
* 지정하든 생략하든 메소드는 모두 abstract public 타입이다\(생략가능\)
* 객체를 생성할 수 없다
  * ```java
    new PhoneInterface()
    ```
  * 이렇게 위에서 인터페이스로 선언한 PhoneInterface를 객체로 생성하면 오류남
* 다른 인터페이스에 상속될 수 있다

## 인터페이스 상속

인터페이스간의 상속을 받을 수 있고, extends 키워드를 사용해서 새로운 규격을 추가한 새로운 인터페이스를 생성 할 수 있다.

```java
interface MobilePhoneInterface extends PhoneInterface{
    void snedSMS();
    void receiveSMS();
}
```

* 기존의 인터페이스를 상속하고 새로운 추상 메소드를 추가함
* 결론적으로 MobilePhoneInterface는 Buttons 상수, sendCall\(\), receiveCall\(\), sendSMS\(\), receiveSMS\(\) 이렇게 5개의 멤버를 가지게 된다
* 추가적으로 인터페이스의 다중 상속을 허용한다
  * ```java
    interface MP3Interface{
        void PlayMusic();
    }
    ```
  * 가 있다고 가정하면
  * ```java
    interface MusicPhoneInterface extends MobilePhoneInterface, MP3Interface{
        void playMP3();
    }
    ```

    * MobilePhoneInterface와 MP3Interface를 상속받고 있고
    * MusicPhoneInterface는 이렇게 7개의 멤버를 사용할 수 있다.
      * MobilePhoneInterface에서 5개의 멤버, MP3Interface에서의 1개의 멤버, 자신\(MusicPhoneInterface\)에서의 1개의 멤버

## 인터페이스 구현하는 방법, 인터페이스 레퍼런스를 통해 구현체를 사용하는 방법

인터페이스의 구현이란 인터페이스의 추상 메소드를 모두 구현한 클래스를 작성하는 것

implements 키워드와 함께 사용

```java
class SamsungPhone implements MobilePhoneInterface{
    public void sendCall(){...}
  public void receiveCall(){...}
  public void sendSMS(){...}

  public int getButtons(){...}
}
```

* 위에서의 정의하는 단계에서 구현해둔 MobilePhoneInterface를 implement메소드를 사용
* implement하게되면 인터페이스의 모든 메소드를 구현해야한다 =&gt; 만약 추상 메소드 중 하나라도 빠트린다면 컴파일 오류
* 추가적으로 getButtons\(\)메소드를 추가 / 추가적인 메소드를 추가할 수 있음

## 인터페이스의 기본 메소드 \(Default Method\), 자바 8

* 기존의 인터페이스는 메소드 정의만 할 수 있고, 구현은 할 수 없었는데 Java8부터 Default Method라는 개념이 생겨서 구현 내용도 인터페이스에 포함시킬 수 있음

기존의 인터페이스 구현 방법

```java
public interface Phone{
    public void call();
}
```

Default Method를 사용하게되면 구현 내용을 인터페이스 안에서 포함 시킬 수 있음

```java
public interface Phone{
    public default void call(){
    System.out.println("Calling...");
  }
}
```

* 메소드 이름의 앞에 default 키워드를 입력하고 메소드 내에 구현 내용을 추가하면됨

인터페이스의 다중 상속으로 인한 오류

* 인터페이스의 경우는 다중 상속이 가능하고 default 메소드의 경우에도 에러가 날 수 있음

```java
public interface FirstMethod{
    default boolean test(){
        return true;
    }
}

public interface SecondMethod{
  default boolean test(){
    return true;
  }
}
```

위처럼 TEST라는 메소드를 제공하는 인터페이스가 2개 있고 이 두 인터페이스를 모두 상속받고 있는 경우에

```java
public class ChildMethod implements FirstMethod, SecondMethod{

}
```

=&gt;어떤 default 메소드를 사용할지에 대해서 모호한 상황

### 해결방안

명시적으로 기재함으로써 모호성을 해결할 수 있음

```java
public class ChildMethod implements FirstMethod, SecondMethod{
    FirstMethod.super.test();
  Second.super.test();
}
```

## 인터페이스의 static 메소드, 자바 8

Java8부터 인터페이스에 static 메소드를 선언할 수 있게 되었다

```java
public interface Phone{
    static void message(){
        System.out.println("Sending Message");
    }
}
```

* 메소드의 맨 앞에 static 키워드를 사용해서 선언

```java
public class Main{
    public static void main(String[] args){
        Phone.message();
    }
}
```

* static메소드를 사용하는 방법은 interface이름.메소드 방식으로 호출해야한다

## 인터페이스의 private 메소드, 자바 9

private method / private static method라는 새로운 기능

단점

* 내부의 특정 기능을 처리하는 내부 method일 뿐이지만 외부에 공개되는 public method로 처리를 해줘야하는 단점이 있음
* 인터페이스를 구현하는 다른 인터페이스 또는 클래스가 해당 메소드에 접근하거나 상속하는 것을 원하지 않는다

중복 코드를 피하고 interface에 대한 캡슐화를 유지할 수 있음

```java
public interface IMyInterface{
    private void method1(String arg){

    }
    private static void method2(Integer arg){

    }
}
```

* 이렇게 접근지정자를 지정해줄 수 있다

### 참고

명품 JAVA PROGRAMMING - 황기태

[https://wedul.site/320](https://wedul.site/320)

[https://www.popit.kr/나만-모르고-있던-java9-빠르게-보기/](https://www.popit.kr/나만-모르고-있던-java9-빠르게-보기/)

