
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
#하나 더 뭐였지..
```

일단 2개로 넣어둔 상황에서 지속해서 <br>
Could not open JPA EntityManager for transaction; nested exception is org.hibernate.exception.JDBCConnectionException: Unable to acquire JDBC Connection <br>
요 에러가 수우우우우우우도 없이 발생하고 있었다 <br>
<br>
그래서 자세한 로그를 확인해본 결과, 해당 에러는 HikariCP 에서 connection timeout이 발생했다고 말해주고 있었고, 해당 connection timeout은 <br>
hikari에서 connection pool을 찾는 도중에 deadlock이 발생하면서 나온 에러로 확인되었다 <br>
<br>

그럼 해당 에러를 재현해보자 <br>

해당 메소드의 내부 로직을 봐보면 이러하다 <br>
메인 메소드에서 데이터를 저장 >> @Async에서 데이터를 저장 >> @Transactional(propagation=REQUIRES_NEW) 에서
findById.ifPresent{it.카운트++} (근데 이게 저장까지 되는게 신기하네) >> 추가로 전에 작성한 findById에는 @Lock(LockModeType.PESSIMISTIC_WRITE)이 달려있었다 <br>
그래서 이 메소드들의 의심점을 가지고 무엇이 문제인지 확인해보았다 <br>
<br>

