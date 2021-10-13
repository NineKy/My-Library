# Week7(패키지)

## package 키워드

자바에서 패키지란 서로 관련 있는 클래스나 인터페이스의 컴파일 된 클래스 파일 들을 한 곳에 묶어 둔 것이다.

예를 들어서 프로젝트 하나에 FileIO작업, Graphic작업, UI작업이 있다고 가정을 해보면 이러한 형식으로 나눠서 사용할 수 있다

![](../.gitbook/assets/package1.jpg)

jdk는 개발자에게 많은 클래스 파일들을 패키지 형태로 제공한다 => 이 패키지들은 rt.jar 압축 파일로 제공된다

자바에서 제공하는 기본 패키지 : 자바 API라고 부르며 jdk를 설치한 디렉토리 밑의 rt.jar파일에 담겨 있다

![](../.gitbook/assets/rt.jar.png)

자주 사용되는 패키지

* java.lang : 기본적인 클래스와 인터페이스를 제공하고, 자동으로 import 되어있다(import는 다음에서 설명)
  * Object클래스 : 모든 클래스에 강제로 상속되고, Object만이 아무 클래스도 상속받지 않는 유일한 클래스로 계층 구조 상 최상위 클래스이다
    * boolean equals(Object obj) : obj가 가리키는 객체와 현재 객체를 비교하여 같으면 true리턴
    * String toString() : 현 객체에 대한 문자열 표현을 리턴
  * String클래스 : 문자열을 나타낸다
    * char charAt(int index) : index 인덱스에 있는 문자열 리턴
    * int compareTo(String anotherString) : 두 스트링을 사전 순으로 비교하여 두 스트링이 같으면 0리턴
    * String concat(String str) : 현재 스트링 뒤에 str 스트링을 덧붙인 새로운 스트링 리턴
    * int length() : 스트링의 길이 리턴
    * String split(String regex) : 정규식에 일치하는 부분을 중심으로 스트링을 분리, 분리된 스트링들을 배열로 저장하여 리턴
    * String trim() : 스트링 앞뒤의 공백 문자들을 제거한 스트링을 리턴
  * Math클래스 : 기본적인 산술연산을 제공하며, 멤버 메소드는 static타입
* java.util : 날짜, 시간, 벡터, 해쉬맴 등 다양한 유틸리티 클래스와 인터페이스를 제공
* java.io : 키보드, 모니터, 파일 등의 입/출력 하는 클래스와 인터페이스를 제공
* java.awt, javax.swing : GUI프로그래밍에 필요한 클래스와 인터페이스를 제공

패키지 생성시, 이름 주의사항

* java : 자바 기본 패키지
* javax : 자바 확장 패키지
* org : 비영리단체(오픈소스)의 패키지
* com : 영리단체(회사)의 패키지
* 패키지의 이름은 소문자
* 자바의 예약어(string, for, switch 등)은 불가능하다

## import 키워드

응용프로그램에서 다른 패키지에 있는 클래스를 사용하고자 한다면 패키지 명을 포함하는 경로명을 사용해야 한다

```java
//메인에서 사용하고 싶은 Scanner를 이용하기 위해서 해당 패키지가 있는 경로를 적어준 모습이다
import java.util.Scanner;
//util 패키지에 있는 여러가지의 클래스를 한꺼번에 import시키기 위해서 작성한 모습이다
import util.*;


public class Main {
    public static void main(String[] args) {
        java.util.Scanner scanner = new java.util.Scanner(System.in);
        System.out.println(Scanner.next());
    }
}
```

이렇게 하게되면 컴파일러가 클래스 파일의 위치를 찾아서 가져다 주게 된다

## ClassPath : 클래스를 찾기 위한 경로

자바환경에서 컴파일 시 바이트 코드로 변환되고, 런타임시 클래스 로더에 로딩하기 위해서 위치를 찾게 되는데 해당 파일의 위치를 찾는 기준이 클래스 패스이다

classpath을 지정하는 두 가지 방법

테스트를 위한 코드

```java
public class Main {
    public static void main(String[] args) {
        System.out.println("HELLO WORLD");
    }
}
```

![](../.gitbook/assets/cp0.png)

* 방법1 : 클래스 경로를 나타내는 환경변수
  * 환경변수의 이름을 CLASSPATH으로 해서 클래스패스의 위치를 넣어주면 jvm이 이것을 참조해서 클래스를 로드할 수 있게 된다
  * 환경변수를 지정해두면 -classpath옵션을 사용하지 않아도 괜찮다 하지만 os가 변경되면 사라지는 점이 있다

![](<../.gitbook/assets/cp1 (2).png>)

![](../.gitbook/assets/cp2.png)

![](../.gitbook/assets/cp3.png)

* 방법2 : java -classpath 옵션
  * 옵션의 맨 앞에는 ' . '이 있으며 이는 java 명령어가 실행되어 실행할 클래스를 찾을 때 현재 디렉토리를 우선적으로 찾도록 지시한다
  * 그 뒤의 경로들은 ' : '을 기준으로 나누어서 찾게 된다 ( 윈도우에서는 ' ; ')
  * 따로 실행시에만 -classpath옵션으로 사용가능

![](<../.gitbook/assets/cp4 (1).png>)

## 접근 지정자

* 필드나 메소드에 붙게되는 접근할 수 있는 수준이다.
* public, protected, default, private이 존재한다
  * public은 클래스내부, 동일패키지, 하위클래스, 그 밖의 영역에서 사용가능
  * protected는 클래스내부, 동일패키지, 하위클래스에서 사용가능
  * default는 따로 지정해주지 않는 경우이고 클래스내부, 동일패키지에서 사용가능
  * private은 클래스 내부에서만 사용가능

## 참고

명품 JAVA PROGRAMMING - 황기태

[https://jjunbbang.tistory.com/8](https://jjunbbang.tistory.com/8)
