# 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라
<br><br>

사용하는 자원에 따라서 동작이 달라지는 그러한 인스턴스 같은 경우에는 새로 생성하지말고 주입받아서 사용하라... <br>
뭐 우선적으로 클래스를 사용하기 위해서 계속해서 인스턴스화를 통해 새롭게 생성해서 사용하는 것은 <br>
재사용성도 떨어지며, 자원도 상당히 많이 잡아먹기도 하는 단점이 존재한다 <br>
<br>

객체를 생성해서 사용하기보다는 내부에 이렇게 선언해두고 사용한다면, Book 이라는 객체가 변화되더라도 Library을 재사용할 수 있다 <br>
```java
public class Library{
    private final Book book;
    
    public Library(Book book){
        this.book = book;
    }
    
    public Book checkTitle(String title){
        return book.getBookByTitle(title);
    }
}
```
<br><br>

뭔가 그래서 이상한게 그냥 객체를 생성하나 주입을 하나 메소드가 바뀌면 어쩌나 했었는데, 이게 뭔가 했더니 Book, 즉 주입하는 대상이 interface 인 경우이다 <br>
인터페이스이기 떄문에 메소드가 바뀔 일도 없으며 지속적으로 재사용하는 것이 가능하다는 점이다 <br>
<br>

이걸 조금 더 좋게 사용?하는 방법으로는 의존성(자원)을 직접적으로 생성자에 주입받는 것이 아닌, 의존성(자원)을 만들어주는 Factory 메소드를 생성하는 방법이다 <br>
여기서 Supplier 인터페이스를 아래와 같이 사용하는 것이 가장 베스트이다 <br>
```java
...

    public Library(Book book){
        this.book = book;
    }
    
    public Library(Supplier<? extends Book> bookSupplier){
        this.book = bookSupplier.get();
    }

...
```
여기서도 굳이 Book으로 타입을 강제하지 않고, Book은 인터페이스 타입이기 때문에 Book으로 부터 만들어진 모든 객체로 부터 주입받을 수 있게 하기 위해서 Book 관련 타입으로 지정 <br>
<br>

