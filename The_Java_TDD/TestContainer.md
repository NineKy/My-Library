# TestContainer

테스트를 진행하는데 있어서 실제 db에 적용이 잘 되는지는 솔직히 진짜 같은 환경을 구축해두고 진행해봐야 알 수 있다 <br>
기본적으로 spring에서 db관련된 전략들은 대부분이 각 db의 특성을 따라서 적용이되는데, 이게 문제는 똑같이 적용해두지 않으면 그런 특성에 따라서 되던 것이 안되는 경우가 생길 수 있고 <br>
이런건 절대 사양이다... 로직이 잘못되면 차라리 다행이지 이런건 찾기도 힘든 문제라고 생각한다 <br>
<br>

저런걸 어떻게 처리하냐 -> 진짜 똑같이 구성을 해두는게 가잘 베스트이다 <br>
근데 테스트에서 그러한 디비들을 설정하기 위해서는 항상 그 디비에 접속해서 진행해야 하는 그러한 단점들이 존재하기 마련이다 <br>
여기서 TestContainer가 들어오게 된다면 테스트를 실행할 때 알아서 설정해둔 디비를 켜서 테스트를 진행하고, 테스트가 종료될 때 종료해주고 아주 간단하게 실제 db처럼 테스트할 수 있는 장점이 있다 <br>
<br><br>

의존성은 뭐 maven repository에서.. <br>
```java
// https://mvnrepository.com/artifact/org.testcontainers/junit-jupiter
testImplementation 'org.testcontainers:junit-jupiter:1.16.2'
```
그리고 어떠한 db를 사용할 것인지 https://www.testcontainers.org/modules/databases/mysql/ 요기에서 modules에서 mysql을 찾아옴 <br>
```java
testImplementation "org.testcontainers:mysql:1.17.2"
```
이외에도 rabbit이나 kafka, ngnix등 다양한 모듈이 있으니까 필요할 때 찾아 쓰자 <br>
<br>

이론적으로의 사용방법은 이러하다 <br>
dependency에 추가해주면 해당 객체가 생성된다 <br>
```java
    static MySQLContainer mySQLContainer = new MySQLContainer().withDatabaseName("testName");

```
객체를 생성하는데 있어서 이런 객체를 한번에 만들어놓고 사용하는게 좋을듯 -> 그래서 static으로 넣어줬는데 굳이 static 아니더라도 baseTest만드는 것도 방법일듯 <br>
<br>

그래서 다음으로는 @BeforeAll, @AfterAll의 메소드 내부에서 .start(), .stop()을 통해서 해당 container를 올리고 내리면 된다 <br>
하지만 문제는 properties를 어떻게 세팅하느냐의 문제이다 <br>
이게 그냥 해놓고 돌리면 매 테스트마다 포트가 변경되며 실행되기 때문에 나름 정해두긴 해야한다 <br>

그 방법으로는 properties에서 'tc'라는 키워드를 넣어주는 것이다 <br>
<br>

이외에도 다양한 설정들을 추가하는 것이 가능한데 -> 뭔가 가장 유용한 건 실시간 로그 스트리밍이다 <br>
```java
    Slf4jLogConsumer consumer = new Slf4jLogConsumer(log);
    mySQLContainer.followOutput(consumer);
```
이렇게 해주면 실시간 로그가 현재 스프링부트에서 뜨게되는 그러한 좋은 방법이된다 <br>
또한 연결된 컨테이너를 @ContextConfiguration이라는 키워드를 통해서 해당 컨테이너의 속성을 가져오는 것이 가능하다 <br>
<br><br>

docker의 compose란? <br>
docker에서 한 가지 이상의 이미지를 동시에 실행하는데 있어서 동시에 이미지들을 관리해주는 툴이다 <br>
docker-compose.yaml에다가 구현하는 방식이다 <br>


<br><br><br><br><br><br><br><br><br><br>