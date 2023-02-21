
이번에 발생했었던 이유 사항에 대해서 정리해보자 <br>
계속해서 HikariCP 에서 deadlock으로 인해서 timeout 에러가 발생하고 있었다 <br>
<br>

우선 문제는 명확했다 <br>
우리 회사는 MSA 구조로 되어있는 터에 수 많은 컴포넌트들이 하나의 디비를 바라보고 있는 상황이다 <br>
물론 디비의 상세 구조를 내가 알고 있는 것은 아니라서 확실하게는 모르겠지만 수 많은 컴포넌트들이 하나의 디비를 바라보고 Connection pool 을 잡고 있어서 
Connection pool이 가득찼던 이슈 사항이 있었다. <br> 
그래서 이걸 해소하기 위해서 개발계에서 많이 사용되는 컴포넌트가 아닌 사용량이 적어도 괜찮은 컴포넌트의 커넥션 풀을 줄이자는 이야기가 나왔다 <br>
물론 해당 작업은 본인이 진행하지는 않았지만 <br>
**일정 컴포넌트의 Hickri의 connection pool size을 2개로 줄였다** <br> 
해당 설정과 관련해서 수정한 config은 이하와 같다 <br>
```yaml
spring.datasource.hikari.maximumPoolSize=2
spring.datasource.hikari.minimum-idle=2
```

일단 2개로 넣어둔 상황에서 지속해서 <br>
Could not open JPA EntityManager for transaction; nested exception is org.hibernate.exception.JDBCConnectionException: Unable to acquire JDBC Connection <br>
요 에러가 수우우우우우우도 없이 발생하고 있었다 <br>
<br>
그래서 자세한 로그를 확인해본 결과, 해당 에러는 HikariCP 에서 connection timeout이 발생했다고 말해주고 있었고, 해당 connection timeout은 <br>
hikari에서 connection pool을 찾는 도중에 deadlock이 발생하면서 나온 에러로 확인되었다 <br>
<br>

그럼 해당 에러를 재현해보자 <br>
해당 에러를 재현하기는 쉬웠다 <br>
하나의 api가 있는데 요것은 req부터 res까지 필요한 시간은 0.7초? 정도면 수행되는 api 였다 그런데 요 0.7초 내에 해당 api를 또 다시 호출하게되면 (같은 row에 접근하는 케이스가 아니더라도 괜찮았다) connection pool을 기다리다가 hikari에서 connection pool을 기다리는 기본 타임아웃 시간 초인 30초를 넘어가면서 타임아웃이 발생하게 되는 그런 상황이였다 <br>

해당 메소드의 내부 로직을 봐보면 이러하다 <br>
메인 메소드에서 데이터를 저장 >> @Async에서 데이터를 저장 >> @Transactional(propagation=REQUIRES_NEW) 에서
findById.ifPresent{it.카운트++} (근데 이게 저장까지 되는게 신기하네) >> 추가로 전에 작성한 findById에는 @Lock(LockModeType.PESSIMISTIC_WRITE)이 달려있었다 <br>
그래서 이 메소드들의 의심점을 가지고 무엇이 문제인지 확인해보았다 <br>
<br>

가장 처음으로 의심을 가져보려고 했던 것은 해당 컴포넌트에게 특별하게 길게 실행되는 쿼리가 있는가 였다 <br>
그야 당연히 connection pool size를 줄인 것은 좀 되었고 해당 컴포넌트에 개발건은 몇 개월동안 없었는데 왜 이제와서..? 갑자기 커넥션 풀을 잡지 못한다고 그러는걸까? <br>
이에 대해 생각해본 답변은 그야 갑자기 요 컴포넌트에서 배치성으로 길게 돌거나 대용량 데이터를 밀어넣는 곳이 있을까 싶어서 내부 코드를 봤지만 따로 그런건 없었다 <br>

그럼 이제는 정말 로직적으로 이슈가 있다는 것에 의심을 가지기 시작했고 <br>
처음으로 의심을 가졌던 것은 @Async 메소드 부분이였다 <br>
사실 따로 @Async였기 떄문에 처음으로 의심을 가진 것은 아니구, 메인 메소드에서 save가 일어나는 부분 다음으로 관련된 메소드였기 떄문에 의심을 해보았다 <br>
실제로 저장하는 메소드가 @Async 내부에 존재하지는 않았지만 비동기 처리가 되게 되면 별도의 스레드가 하나 더 생기게 되고 비동기 처리로 인해서 생긴 스레드에서 connection pool을 요구하게 된다 그래서 connection pool을 얻기위해서 기다릴 것이라고 생각했었다 <br>
요걸 확인해보기 위해서 해당 @Async문을 삭제하고 다시 테스트를 진행해 봤지만? 결과는 같았다 <br>
곰곰히 생각해보면 비동기 메소드에서 connection pool을 얻기 위해서 hikariCP에서의 대기열인 blocking queue에 들어갔다고 한들, 원래의 메소드가 30초가 걸리지 않고 connection pool을 뱉어낼텐데 요건 문제가 아니겠거니 싶었다 <br>
간단하게 chatGPT에게 blocking queue에 대해서 물어보니 답변은 이러했었다 <br>
A blocking queue is a queue that blocks when you try to dequeue from it and the queue is empty, or when you try to enqueue items to it and the queue is full. A blocking queue uses locking and signaling mechanisms to provide a thread-safe, synchronized way for producer and consumer threads to communicate and synchronize with each other. <br>

그럼 그 다음을 문제는 @Transactional(propagation=REQUIRES_NEW) 였는데 요거겠거니 했었다 <br>
@Transactional 애노테이션에 대해서는 한번 공부를 하긴 했었는데 간단하게 다시 한 번 정리해보자<br>
~~ 정리 ~~ 
우선 propagation 속성에서 REQUIRES_NEW 이라는 속성은 기존에 진행되던 트랜잭션 이외에 새로운 트랜잭션을 생성해서 진행하겠다는 의미이다 <br>
따라서 해당 애노테이션이 달려있는 메소드 내부에서는 새로운 트랜잭션이 실행되고 소스코드에서 해당 메소드 내부에서는 findById.ifPresent{ it.카운트++ }가 되어있었는데 여기 메소드에서 새로히 connection pool을 얻고 디비를 조회하고 수정을 하고 update 쿼리를 날리는 로직이다. <br>
그러면 결국은 여기에서 새로히 connection pool을 요청하게 되는 결과이고, 하나의 큰 메소드에서 총 2개의 connection pool을 요한 다는 것을 확신할 수 있었다 <br>
<br>

그래서 어디에서 dead lock이 발생해서 타임아웃이 나는가? 에 대해서 고민해보았는데 생각해보면 이렇다 <br>
처음에 언급한 api을 0.7초 내로 동시에 2개를 전송하게 되면 각각의 api가 시작되면서 메인 엔티티를 저장하기 위해서 conneciton pool 을 각각 하나씩 총 2개의 커넥션 풀을 사용하게 된다 
그 상황에서 메소드가 끝나지 않은 상황에서 @Transactional(propagation=REQUIRES_NEW)을 통해서 새로운 트랜잭션을 시작하고 그 새로운 트랜잭션에서 동기적으로 conneciton pool을 얻기 위해서 hikariCP의 connection 대기 시간의 최대치인 30초를 기다리게 되는데, 애초에 처음에 메인 엔티리를 저장하기 위해서 열어두었던 connection pool 2개가 원래라면 메소드가 종료됨에 따라서 connection pool을 반납해야만 하지만
해당 메소드가 끝나지 않았기 떄문에 계속해서 connection pool을 열어둔 상황으로 기다리는 것이고 그럼 각각에서 REQUIRES_NEW로 파생된 트랜잭션에서 30초를 기다리다가 타임아웃이 발생하게 된 것이다!!! <br><br>

그래서 요걸 어떻게 해결할 것인가에 대해서 고민을 해보았다 <br>
우선은 REQUIRES_NEW가 들어간 부분에서 이슈가 있었기 떄문에 요걸 지우고 테스트를 해보니 잘 되었었지만 본인이 만든 코드도 아니고 해당 코드에 이유가 있어서 그렇게 애노테이션을 달아두었을 것이기 때문에 소스 코드를 수정하는 것은 불가능했다 그렇기 떄문에 적어도 4개의 conenciton pool이 필요하다고 판단했고 맨 처음에 설정해두었던 connnection pool의 사이즈를 2개에서 4개로 늘렸고 4개로 늘려둔 상황에서 REQUIRES_NEW을 통한 테스트도 정상적으로 통과하게 되었다 <br>
<br>

정말 분석하는데 트랜잭션에 대한 개념도 다시 한번 고민하게 되는 시간이였고, 맨 처음에 REQUIRES_NEW 을 유지하면서 교착상태를 해결하기 위해서 isolation level을 건드려보려고 했었지만 해당 방법을 사용하는 것은 위험한 방법이라고 들었다 <br> 그래도 이김에 isolation level에 대해서 배울 수 있는 시간이였다 <br>

원래는 dead lock을 방지하기 위해서 @Lock을 통해서 진행해보려고 까지 생각했었다. <br>
다시 생각해보면 @Lock까지 갈 필요도 없는 문제였는데 단순히 내가 @Lock의 lockMode에 대해서 자세하게 알지 못했기 때문에 그렇게까지 생각이 진행되었던 것 같다<br>
이 귀중한 기회를 날리지말고 공부할 수 있는 시간으로써 @Transactional, @Lock에 대해서 정리 다시 한 번 해보도록 하자<br><br>


