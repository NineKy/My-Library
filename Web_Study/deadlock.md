
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

