# Week3\(연산자\)

## 산술 연산자

* 숫자 값을 피연산자로 받아서 하나의 숫자 값을 반환한다. 종류로는 +,-,\*,/,% 가 있다.
* 덧셈\(+\) : 숫자 피연산자를 더한 값, 문자열이면 문자열을 연결한 값
* 뺄셈\(-\) : 숫자 피연산자를 뺀 값
* 곱셈\(\*\) : 숫자 피연산자를 곱한 값
* 나눗셈\(/\) : 숫자 피연산자를 나눈 몫
* 나머지값\(%\) : 숫자 피연산자를 나누고 남은 나머지
  * ```text
    형태 : 변수/상수 + 변수/상수 
    public class Main {
        public static void main(String[] args) {
            int a=10, b=5;
            //int+int = int
            System.out.println(a+b);//15
            //String+int = string
            System.out.println("this" + a);//this10
            //String + String = String
            System.out.println("this" + "is ");//thisis

            System.out.println(a-b);//5
            System.out.println(a*b);//50
            System.out.println(a/b);//2
            System.out.println(a%b);//0

            //+=, -=도 사용이 가능하다 
            a = a + 10;//20
            a+=10;//20
            b=b-3;//2
            b-=3;//2
        }
    }
    ```

## 비트연산자

데이터를 비트단위로 연산

\*비트 단위이기 때문에 0과 1로 표현이 가능하다.

* 비트 이동 연산자
  * x&lt;&lt;y : 정수 x의 각 비트를 y만큼 왼쪽으로 이동 / 빈자리는 0으로
  * x&gt;&gt;y : 정수 x의 각 비트를 y만큼 오른쪽으로 이동 / 빈자리는 
  * x&gt;&gt;&gt;y : 정수 x의 각 비트를 y만큼 오른쪽으로 이동 / 빈자리는 0으로
* 비트 논리 연산자\(비트를 다 쪼개서 자리별로 비교\)
  * & - AND - 둘다 1일경우만 1
  * \| - OR - 둘 중 하나만 1일경우만 1
  * ^ - XOR - 둘 중 하나는 1이고 다른 하나가 0인경우만 1
  * ~ - NOR - 비트를 거꾸로

![](../.gitbook/assets/bit_oper.png)

## 관계연산자

좌항과 우항을 비교 후, 결과에 따른 boolean값을 반환

* &lt;, &gt; : 좌항과 우항을 비교해서 결과값이 부등호가 입을 벌리고 있는 쪽이 크다면 true, 아니면 false
* &lt;=, &gt;= : 위와 똑같은 기준으로 true와 false를 리턴하지만 좌항과 우항이 같은 경우에도 true를 리턴
* == : 좌항과 우항의 값만을 비교하여 결과를 리턴
* ```text
  public class Main {
      public static void main(String[] args) {
          //1==1이 true, 그것을 !(부정)했기 때문에 false
          System.out.println(!(1==1)); //false
          //1!=1이 false 그것을 !(부정)했기 때문에 true
          System.out.println(!(1!=1));//true
      }
  }
  ```

## 논리연산자

논리식을 보고 결과에 따른 boolean값을 반환

* && - AND : 논리식이 모두 true여야 true반환
* \|\| - OR : 논리식 중 하나가 true면 true반환
* ! - NOT : 논리식의 결과의 반대 boolean값 반환

## instanceof 연산자

참조변수가 instanceof로 형 변환이 가능한 타입인지 연산한다

​ 만약 형 변환이 가능하면 true, 불가능하면 false

* 객체 + instanceof + 클래스
* 객체가 클래스가 될수있냐? 를 물어보는 연산자
* ```text
  class Parent{}
  class Child extends Parent{}
  public class Main {
      public static void main(String[] args) {
          Parent p = new Parent();
          Child c = new Child();

          System.out.println("p객체는 Parent가 될수있냐? : "+(p instanceof Parent));//true
          System.out.println("p객체는 Child가 될수있냐? : "+(p instanceof Child));//false
          System.out.println("c객체는 Parent가 될수있냐? : "+(c instanceof Parent));//true
          System.out.println("c객체는 Parent가 될수있냐? : "+(c instanceof Child));//true
      }
  }
  ```

## assignment\(=\) operator

할당 연산자 : 변수에게 값을 할당할때 사용되는 연산자

* 변수에 값을 대입하는 변수=값 형태
* 연산자에서도 언급한 +=, -=, \*=, /= 형태
* 추가적으로 %=, &=, \|=, ^=, &gt;&gt;=, &lt;&lt;= 형태가 있다. 하지만 모든 형태는 아래와 같은 의미를 가지고 사용된다.
  * 변수 %= 값
  * 변수 = 변수%값

## 화살표\(-&gt;\) 연산자

람다 표현식에서 화살표 연산자를 사용한다.

람다 표현식이란 함수를 하나의 식으로 표현한 식이다.

* 함수를 람다 표현식으로 표현하게되면 클래스 작성 후, 객체를 생성하지않아도 사용이 가능
* 하지만 클래스의 선언과 동시에 객체가 생성되는 원리로 단 하나의 객체만을 사용할 수 있다.
* 이것을 익명 클래스라고 한다.
  * 익명\(무명\)클래스 : 인터페이스를 구현시, 재사용성이 없는 클래스파일을 생성할때 사용된다.
  * ```text
    interface Test{
        public void go();
    }
    public class sampleClass{
        public static void main(String[] args){
            Test test = new Test(){
                public void go(){
                    System.out.println("go");
                }
            };
            test.go
        }
    }
    ```
  * 익명 객체는 단독으로 생성할 수 없고, 클래스를 상속하거나 인터페이스를 구현해야만 가능하다.
  * 형태를 잘 기억해두자!
* 람다 표현식의 작성은 \(매개변수목록\) -&gt; {함수} 방식이다.
* 주의점
  * 매개변수의 타입이 추론가능하면 타입 생략가능
  * 함수가 하나의 명령문으로 이루어졌으면 {} 생략가능/명령문에 ; 안붙힘
  * 함수의 몸체가 하나의 return문이면 {} 생략가능
  * return대신 표현식으로 사용가능하고 반환값은 표현식의 결과값
* 함수형 인터페이스 : 람다 표현식을 하나의 변수에 대입할때 사용하는 참조 변수의 타입
  * @FunctionalInterface 라는 어노테이션을 사용해서 명시하게되면 컴파일러는 해당 인터페이스가 함수형 인터페이스라고 인식되고, 함수가 2개이상존재한다면 에러를 발생
* ```text
  //일반적인 함수
  int min(int x, int y){
      return x<y?x:y;
  }
  //람다표현식
  (x,y) -> x<y?x:y;
  //가시적으로도 매우 편안하다!
  ```

## 3항 연산자

조건연산자라고도 불리고, 코드의 양을 줄여준다는 장점이있음

condition에 따라 분기처리를 하는 코드일 경우에 깔끔하게 표현이 가능함

* 변수 = \(boolean값을 리턴하는 것\) ? \(true시 진행\) : \(false시 진행\);
* ```text
  int test;
  if(111 > 222){
          test=111;
  }else{
          test=222;
  }

  //3항 연산자를 사용
  int test = (111>222) ? 111 : 222;
  ```
* 차이
  * if/else : statement =&gt; 값이 생성되는 개념이 아님
  * 3항 연산자 : expression =&gt; 값이 생성된다.
    * =&gt;많은 parameter를 전달하는 메소드를 호출할때 상황에 인해 변화되는 값을 넘겨줄때 사용하면 좋다고하는데... 기억하고 있다가 활용해보도록하자

## 연산자 우선순위

여러가지의 연산자가 하나의 식에 들어있을때 우선순위를 지켜서 계산이 된다.

* 괄호&gt;산술&gt;비교&gt;논리&gt;대입 순서이다.
  * 괄호/대괄호 : \(\), \[\]
  * 부정/증감연산자 : !, ~, ++, --
  * 곱/나눗셈연산자 : \*, %, /
  * 덧셈/뺄셈연산자 : +, -
  * 비트이동연산자 : &lt;&lt;, &gt;&gt;, &gt;&gt;&gt;
  * 비교연산자 : &lt;, &lt;=, &gt;, &gt;=
  * 등가연산자 : ==, !=
  * 비트연산자 : & / ^ / \|
  * 논리연산자 : && / \|\|
  * 조건연산자 : ? :
  * 대입연산자 : =, +=, -=, \*=, /=, %=, &gt;&gt;=, &lt;&lt;=, &=, ^=, !=
  * 콤마 연산자: .
* 여기서 결합되는 순서는 거의 오른쪽\(-&gt;\)방향으로 결합되지만
* 부정/증감연산자와 조건연산자 그리고 대입연산자는 왼쪽\(&lt;-\)방향으로 결합된다.

## Java 13

JDK 13이 나오고 문서에서 언급한 주요 기능들은

* Application Class-Data Sharing\(CDS\)가 java 애플리케이션의 실행이 종료될때 동적으로 작동하도록 향상
* ZGC\(Z Garbage Collector\)가 오랫동안 사용이 안된 heap메모리를 운영체제에 돌려주도록 수정
* Legacy Socket API를 재구현 =&gt; 미래의 통신망에서는 스레드를 blocking보다는 park시키는 방식이 대세라고 생각되어 재구현해서 넣었다
* Text Block : 자바에서도 2차원 텍스트 블록을 사용할 수 있다. = 공백을 """의 위치를 통해서 조절할 수 있다.
* Switch연산자의 변화도 존재 - switch연산자의 설명 이후에 추가설명

## Switch연산자

경우의 수가 많아지게 되면 if/else문도 많아지게 되니까 이럴때는 switch문을 사용

하지만 정수,문자 값을 리턴하는 조건식만이 사용가능하다.

* 단계
  * 조건식을 계산
  * 결과값에 걸린 case문으로 가서 
  * case의 명령을 실행
    * 만약 결과값에 걸린 case문이 없다면 default문으로 이동해서수행한다.
  * break문을 만나면 switch문에서 빠져나감
    * 여기서 break을 걸지 않는다면 다른 break문을 만날때까지 or switch문의 끝까지 수행한다. =&gt; 여러가지를 동시에 처리할때도 사용한다.
* jdk 13에서 switch문에 대해 추가된 표현들
  * 화살표
    * 원래는 case 결과값: 이렇게 사용함 =&gt; :를 붙힌 다음에 해당 case문의 명령들을 나열했는데
    * case 결과값 -&gt; 명령어
    * 이렇게 사용할 수 있음
  * 3항 연산자처럼 expression으로 사용할 수 있음
    * ```text
      T result = switch(arg){
          case L1 -> e1;
          case L2 -> e2;
          default -> e3;
      };
      ```
  * yield 키워드 추가
    * return개념과 비슷하다
    * :을 사용하는 switch문에서도 사용가능
    * ```text
      //화살표표현을 사용 + expression을 사용 + yield키워드사용
      int j = switch(day){
          case MONDAY -> 0;
          case TUESDAT -> 1;
          default ->(
              int k = day.toString().length();
              int result = f(k);
              yield result;
              )
      };
      ```

