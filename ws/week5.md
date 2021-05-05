---
description: 클래스
---

# Week5

## 클래스란?

* 객체를 만들어내기 위한 설계도 혹은 틀
* 객체를 생성하는데 사용된다.
  * 여기서 객체란 클래스의 모양대로 동일한 속성을 가지고 만들어지지만, 자신만의 고유한 값을 가짐으로써 구분될 수 있다.
* 구성
  * 클래스선언
    * class 키워드와 클래스이름을 선언하고 중괄호안에 필드와 메소드를 작성\(캡슐화의 원칙을 준수\)
    * 클래스 생성시, 소스파일과 클래스의 이름이 같아야하고, 첫 글자는 대문자로 쓴다
  * 접근 한정자
    * public : 모든 클래스에서 접근 가능
    * default : 오직 같은 패키지의 클래스에서만 접근 가능
    * final : 하위 클래스를 가질 수 없고 새로운 클래스가 상속되어 생성불가능
    * abstract : 추상 클래스의 선언, 추상 클래스는 객체를 생성할 수 없는 클래스
    * synchronized : 스레드를 동기화 할 필요가 있을떄 사용
  * 필드와 메소드
    * 필드 : 객체 내에 값을 저장할 멤버 변수
      * 접근지정자 자료형 변수이름
    * 메소드 :  아래에서 자세히
    * 클래스의 필드와 메소드를 가져오기 위해서는 클래스명.\(필드or메소드\)
  * 생성자
    * 클래스의 이름과 동일한 메소드
    * 객체가 생성될 때 자동으로 호출되는 메소드
* ```java
  //접근권한 클래스선언 클래스이름
  public class Circle{
         //radius와 name은 필드
      public int radius;
      public String name;
      //Circle이라는 클래스의 생성자
      public Circle(){

      }
      //Circle이라는 클래스의 메소드
      public double getArea(){
          return 3.14 * radius * radius;    
      }
  }
  ```

## 객체를 만드는 방법

* 자바에서는 반드시 new 연산자를 사용하여 객체를 생성해준다.
* 객체를 선언을 한 이후에 new키워드를 사용해서 객체를 생성해주면 된다.

```java
//클래스면 객체변수명;  이렇게 객체를 생성해줌
Circle pizza;
//객체변수명 = new 클래스명(); 객체의 주소를 참조변수에 저장
pizza = new Circle();
```

* new 연산자에 의해서 객체가 생성되는 과정
  * Circle타입 크기의 메모리 할당
  * Circle\(\) 생성자 코드 실행\(클래스 생성시 생성자 자동실행\)

## 메소드 정의하는 방법

메소드란 특정 작업을 수행하기 위한 명령문의 집합

함수이고, 객체의 행동을 구현함

하나의 메소드는 하나의 기능만 수행하도록 설계하는 것이 좋음

반복되거나 관련된 코드들을 메소드로 구현해두면 좋다

형태 : 접근지정자 리턴타입 메소드이름

* 접근 지정자
  * 필드나 메소드에 붙게되는 접근할 수 있는 수준이다.
  * public, protected, default, private이 존재한다
    * public은 클래스내부, 동일패키지, 하위클래스, 그 밖의 영역에서 사용가능
    * protected는 클래스내부, 동일패키지, 하위클래스에서 사용가능
    * default는 따로 지정해주지 않는 경우이고 클래스내부, 동일패키지에서 사용가능
    * private은 클래스 내부에서만 사용가능
* 리턴타입
  * 메소드는 return문이 존재하고 return되는 값의 형태를 선언해주는 과정이다 
  * 만약 return이 없었으면 void타입을 지정해주게된다

```java
//접근지정자, 리턴타입, 메소드이름 으로 선언해주고
int radius=3;
int count=0;
//메소드는 하나의 기능을 하도록 설계
public double getArea(){
    //메소드의 리턴되는 값이 double이기 때문에 메소드의 리턴타입을 double로 선언
    return 3.14*radius*radius;
}
public void pplus(){
    //만약 return해주는 값이 없다면 void타입을 사용해준다
    count++;
}
```

## 생성자 정의하는 방법

생성자는 객체가 생성될 떄 객체의 초기화를 위해서 실행되는 메소드임

생성자는 객체가 생성될때 자동으로 호출

이름은 해당 클래스의 이름과 같아야 하며, 리턴값이 없어야한다

만약 따로 정의하지 않는다면 컴파일러가 자동으로 기본생성자를 생성해준다

```java
//기본생성자
class Circle{
    //기본생성자는 매개변수가 없고 아무런 기능도 없이 단순 리턴
    public Circle() { }
}
```

생성자는 여러개 작성\(오버로딩\)할 수 있다

* 매개변수의 개수와 타입이 다르면 그만큼 생성자를 생성할 수 있다.

생성자는 new키워드를 통해서 객체를 생성할 떄 한 번 호출된다.

```java
public class Book{
    String title;
    String author;
    //하나의 인자를 가진 생성자, 클래스 이름과 동일한 생성자
    public Book(String t){
        title = t;
        author = "작가";
    }
    //두개의 인자를 가진 생성자
    public Book(String t, String a){
        title = t;
        author = a;
    }
    //위에 생성자가 2개나 이미 선언되어있기 때문에 main함수에서 new로 객체를 생성할때 기본 생성자를 생성하지 않는다
    public static void main(String [] args){
        Book littlePrince = new Book("어린왕자", "생택쥐페리");
        Book loveStory = new Book("춘향전");
        System.out.println(littlePrince.title + " "+littlePrince.author);
        System.out.println(loveStory.title +" "+loveStroy.author);

    }
}
```

## this 키워드 이해하기

this는 현재 객체 자신에 대한 레퍼런스이다 / 현재 실행되고 있는 메소드가 속한 객체에 대한 레퍼런스이다.

인스턴스 자신을 가리키는 참조변수, 인스턴스의 주소가 저장되어있다.

```java
public class Circle{
    int radius;
    //여기서 this는 현재 객체에 대한 래퍼런스이기 때문에 현재 객체의 맴버인 radius에 접근한다
    public Circle(int r){this.radius = r;}
  //
    public int getRadius(){return radius;}
}
```

this의 필요성 : 매개변수의 이름은 그 자체로써 코드를 읽는 사람에게 용도를 나타낸다. 그래서 적합한 이름이 필요하게 되는데 만약 위의 코드에서

```text
public Circle(int radius)이렇게 매개변수가 r대신 radius가 들어오게 되면
간단하게 보면 public Circle(int radius){radius = radius}가 된다.
```

이렇게 매개변수의 이름을 멤버 변수와 같은 이름으로 붙힐려는 경우가 있는데 이럴때 this가 유용하게 사용될 수 있다.

추가로 메소드에서 자기 자신를 리턴해야하는 경우에도 유용되게 사용될 수 있음

```java
public Circle getMe(){ return this;}
```

