# try-finally 보다 try-with-resources를 사용하라 
<br>

try finally 를 많이 개선한 try-with-resources 이라는 방식을 자바 7이후부터 제공해주고 있다 <br>
<br>

사용하는데 있어서 보면, 만약에 문제가 생겨서 finally에서 닫아야하는 것이 이렇게 2가지가 있다고 가정해보자 
```java
...
InputStream in = new FileInputStream("IN");
OutputStream out = new FileInputStream("OUT");
try{
    //DO SOMETHING
    try{
        //DO SOMETHING
    }
    finally{
        out.close()
    }
} finially{
    in.close()
}
```
이중으로 이렇게 닫는 것이 과연.. 좋은 것일까? <br>
그렇다고 하나의 finally에서 in.close()와 out.close() 을 한꺼번에 닫는 행위는 잘못된 행위이다 <br>
왜냐하면 out.close(); in.close(); 이렇게 있다고 가정해봤을 때 out.close()에서 에러가 난다면? in.close()는 그럼 실행되지 않을텐데? <br>
그렇기 떄문에 이중으로 구현하는 것이 좋은 방법이라고 볼 수는 없는 것이다 <br>
<br><br>

그래서 위의 방식을 대신하는 좋은 방법인 try-with-resources 의 방법은 이러하다 <br>
```java
try(InputStream in = new FileInputStream("IN"); OutputStream out = new FileOutputStream("OUT)){
    //DOSOMETHING
}
```
달라진점은 이러하다, try 내부에서 사용할 것이며, finally 에서 회수해야할 필요가 있는 그러한 객체들을 <br>
try 문을 시작하기 전에 괄호를 열고 그 내부에다가 위의 객체들을 선언해주는 것이다 <br>
이렇게 해두면 따로 close 해줄 필요 없이 close 을 어느정도 보장해주는 그러한 방식이다 <br>
<br><br>

이외에도 장점은 예외 stacktrace 이다 <br>
만약 try 내부에서 예외가 2개가 발생한다고 가정했을 때, 항상 예외가 발생하면서 첫 번째 예외만을 보여주었다 <br>
그 밑의 예외는 첫 번째 예외가 발생했기 떄문에 따로 보여주지 않고 무시되기 마련이였는데, try-with-resource를 사용함으로써, <br>
두 개의 모든 예외가 보이는, 모든 에러가 보여지는 그러한 장점이 존재한다 -> 정말 좋은 기능이다..! <br>
<br>

이렇게 try-finally가 아니라 try-with-resource를 사용하게 되면, 장점이 많다 <br>
첫 번째로 코드가 간결해지고 <br>
두 번째로 굳이 finally를 구현하지 않아도 Close가 보장되며 <br>
세 번째로 내부의 예외가 한 꺼번에 보이며 <br>
네 번째로 이것을 사용하면서도, catch, finally를 구현해서 사용하는 것이 가능하다 <br>
<br>

정말 사용하기 좋은 그러한 기능이라고 생각된다..! <br>
<br><br>



