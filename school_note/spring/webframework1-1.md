---
description: 스프링 MVC에 대한 것들의 노트
---

# Webframework1-1

웹 프레임워크 : Inversion of Control

App구조 : 코드의 상당부분을 제공해준다 -> 핵심 로직에만 집중할 수 있다

스프링

​ Light Weight Java Application Framework

​ POJO - Plain Old Java Object 기반의 엔터프라이즈 App 개발을 쉽고 편하게

Dependency Injection

​ Bean Container가 bean을 만들게 되고 의존성 주입을 실행시켜준다.

​ Inversion of Conrol은 Dependency Injection이라고도 불리운다

The Spring Container의 주된 기능

​ 객체의 생성과 관리 - Inversion of Control

​ 객체의 의존성을 주입 - Dependency Injection

Spring Container의 configuration metadata(환경설정)

​ xml

​ java annotation

​ java-based configuration

* Spring Conter의 종류
  * BeanFactory -> XMLBeanFactroy
  * ApplicationContext -> ClassPathXmlApplicationContext
  * ```java
    ApplicationContext context = new ClassPathXmlApplicationContext("kr/ac/hansung/spring/beans/bean.xml");
    HelloWorld obj = (HelloWorld)context.getBean("helloWorld");
    ```
* Advantages of DI
  * 의존성이 줄어든다 = 변화에 용이
  * 재사용률이 증가한다(Reuseable, Testable, Readable)
* Beans : POJO를 bean이라고 부른다
  * 속성 : class, id, scope(singleton, prototype, request, session), constructor, property, init, destroy
    * scope에 singleton을 넣어주면 스프링컨테이너를 한번만 만들고
    * prototype은 새로운 요청을 받을때마다 생성
  * xml파일에서 선언시 내부에서 태그 안의 속성으로 어떤 빈의 의존성을 주입받을 것인지 정할 수 있따.
    * \<constructor-arg태그 안에 type값을 줘서 type값대로 주입
    * constructor-arg태그 안에 index값을 줘서 자리를 구체화 시킨다
* Annotation을 사용하기 위해서는
  * pom.xml(maven설정파일)에서
    * 태그를 넣어주거나
    * ```
      <context:annotation-config/>
      ```
  * @Required - 뭔가 빠트려도 @Required를 써둔게 안나오면 에러로 알려줌
    * Setter에만 사용이 가능
    * 엄격하게 xml에 존재하는지 확인한다
    * 이렇게 bean안에 넣어주고 @Required를 쓰게되면 setter함수의 property값이 xml에 있는지 확인해준다
  * @Autowired
    * 태그를 넣어준 함수의 타입을 bean에서 찾아서 자동으로 주입
    * 자동주입 대상에 Autowired를 적고 bean을 등록만하면 태그가 필요 없어짐
  * @Qualifier
    * @Qualifier(value=" ")여기 value값을 따라가서 xml에서 를 찾는다.
    * @Autowired를 해주고 @Qualifier를 해줘서 Autowired에서 헷갈리는 것을 처리해줌
  * @Resource(name="") 으로 이름으로 autowiring해준다
    * @Resource는 이름으로 autowire해주고
    * @Autowired는 타입으로 autowire한다
* Maven
  * 원격 repository에서 자동으로 원하는 것을 의존성을 고려해서 다운로드해주는 소프트웨어
  * apache에서 제공하는 sw
  * 자동으로 많은 의존성을 가져올수 있음, 의존성관리툴
  * 과정은 compile -> plugin -> plugin:goal
  * pom.xml에서 작성을 해두고 실행을 하게되면 maven core를 통해 원격저장소에서 필요한 툴들을 가져와서 local repository에 저장하게 된다.
  * 만약 a->b b->c 이면 a->c인데 만약 a에서 c에 관한 툴이 필요없으면 으로 제외하는 것이 가능하다
    * 여기서 만약 b->c인데 c는 필수가 아닌 상태이고 여기서 a->b가 되면 a->c이냐
      * 이건 명시적으로 포함한다고 하지 않으면 포함되지 않는다
* POM(Project Object Model)
  * 설정파일이라고 생각하면 된다
  * 이렇게 3개로 좌표세트이다
* Super POM
  * 모든 POM은 default으로 super POM을 상속받는다.
  * POM간에서도 상속이 존재하고 상속에서의 부모는 로 표시, 여기 안의 에 받을 부모의 이름을 적어준다.
* Build lifecycle : compile -> test -> package -> install -> deploy
* Dependency Mechanism
  * 만약 같은 lib을 의존해서 자동으로 다운로드받게되면 가장 가까운 lib을 의존하게 된다
  * dependency scope :
    * provided : packaging시 빠지게 된다
    * runtime : run, test시에는 필요하지만 compile에는 필요없는 경우 / jdbc같은 파일
    * test : 평소에는 필요없지만 test compile시, execution시 필요

AOP(Aspect Oriented Programming) : 여러개에 거쳐있는 function들을 독립적으로 뺄 수 있음 = 재사용성이 올라감

* 의의는 class들을 분리시켜두었다가 runtime시 주입해서 사용하겠다는 것
* 장점
  * concern의 분리
  * 코드의 재사용성, 모듈화가능
  * 의존성이 약한 디자인
* aop에서의 사용단어들 : Joinpoint, advice, PointCut
  * Aspect : concern을 구성하고 있는 클래스
  * JoinPoint : 우리 application안의 function
  * Pointcut : JoinPoint의 집합, advice가 실행되야 함
  * Advice : 실제로 실행될 메소드
    * before, after, after-returning, after-throwing, round
* @Pointcut("execution(sound())")이렇게달아두고 => Advice에 @Before("@Pointcut해둔 함수") => @Pontcut안에 넣어둔 함수를 실행하게되면 @Before가 실행되고 나서 실행된다
* Aspect의 선언방법으로는
  * XML 설정파일에서 선언하는 방법
    * spring-aspects or aspectjrt을 pom.xml에 선언해둬야함
    *
      1. Aspect을 구현 -> 함수하나를 구현
      2. Aspect을 선언 ->
         1. ```
            <aop:config>
                <aop:aspect id="식별자" ref="1번에 구현한 함수">

            <bean id="1번에 구현한 함수" > 
            bean으로 등록한 후, aspect으로 등록
            ```
      3. Pointcut을 선언
         1. ```
            <aop:config>
                <aop:aspect>
                    <aop:pointcut id="함수"
            ```
      4. advice을 선언
         1. ```
            <aop:config>
                <aop:aspect>
                    <aop:pointcut
                        <aop:before
                        <aop:after
                        <aop:after-returning
                        <aop:after-throwing
            ```
  * @Aspect으로 설정하는 방법
    *
      1. @Aspect주석을 달아준다
      2. @Aspect을 달아준 함수안에 @Pointcut("pointcut 표현식")을 넣어주고
      3. @Aspect을 달아준 함수안에 @Pointcut의 아이디에 해당하는 값를 함수로 만들어준다
      4. @Aspect을 달아준 함수 안에 @Before(함수)를 넣어서 joinpoint를 처리해준다
    * 이렇게 @Aspect을 사용하게되면 한 함수안에서 모든 것을 해결할 수 있다.

Spring JDBC : 라이브러리이고, DAO(Data Access Object)를 통해서 db와 연결한다.

* 사용하기 위한 라이브러리(pom.xml에 넣어줘야하는 artifactId)
  * commons-dbcp2
  * mysql-connector-java
  * spring-jdbc
* JDBC Template
  * spring-jdbc의 중요 클래스중 하나이다
  * 알아서 자동으로 모든 데이터베이스와의 통신을 해결해줌
  * 톰캣에는 request가 올때 처리해줄 스레드를 미리 만들어두고 있는데 DB connection pool에서도 똑같이 미리 db에 접속할때 필요한 스레드를 미리 생성해두고 대기
  * ```java
    beans.xml에서 dataSource라는 빈을 설정하고 
    @Autowired로 dependency injection을 해주고
    public void setDataSource(DataSource dataSource){
        선언을 해주고
        this.jdbcTemplateObject = new JdbcTemplate(dataSource);
    }
    public List<Offer> getOffers(){
        이렇게 쿼리문을 사용
        return jdbcTemplateObject.query("select * from offers");
    }
    ```
  * jdbcTemplateObject.queryForObject(sql문, sql문의 ?에 들어갈 값, 리턴되는 타입)
  * Mapper함수를 이용해서 queryForObject의 결과값인 ResultSet을 매핑해서 원하는 객체로 생성해준다.
    * 따로 Mapper함수 만들어서 사용하는것도 방법이지만 handler방식으로 함수생성해서 사용가능
* JDBC Driver
  * mysql-connecter
* Data Source
  * jdbc.properties라는 파일에 감춰야하는 정보들을 넣어둠
  * beans.xml에서 위의 jdbc.properties를 찾아서 넣어주고 꺼내서 사용하는 방식
* DAO(Data Access Object)
  * 관계형 데이터베이스에 접속하기 위한 객체지향적 API
* JDBC의 종류
  * Plain JDBC : 예외처리를 하기 위해서 필요없는 코드를 반복해서 써야한다
    * sql문같은것도 일일이 적어줘야한다
  * Spring JDBC : 뭐 여러가지 세세한 디테일들을 알아서 처리해준다

Spring MVC Framework -> 웹 애플리케이션의 Model, View, Controller를 제공해줌

* Model : 웹 애플리케이션의 데이터(Plan Old Java Object)
* View : model을 rendering 해주는 담당
* Controller : 사용자의 request를 처리해주는 담당
* Structure
  * Dispatcher Servlet : 모든 request를 가로채서 적절하게 분배
  * Handler Mapping : @RequestMapping을 통해서 적절한 컨트롤러에게 보내줌
  * Controller : 요청을 처리해주고 결과를 모델객체에 붙혀서 view에서 보여줌
  * View Resolver : 이름을 통해서 파일을 찾아줌
  * View : JSP, HTML, XML, Velocity등등
* Configuration
  * POM.xml : maven설정파일
    * spring-webmvc
  * web.xml : 톰캣설정파일
    * \<param-name 으로 contextConfigLocation, \<param-value 으로 xml을 넣어줌
  * root-context.xml / servlet-context / dao-context / service-context : Spring MVC Configuration
    * servlet-context는 정적인 자원을 처리해주고 spring의 DispatcherServlet에서 로딩됨
  * \<annotation-driven : annotation을 사용하겠다고 알려줌
  * \<resource mapping=... : 정적인자원을 매핑해줌
  * Bean InternalResourceViewResolver
    * prefix에서 어느 위치에서 참고할것인지
    * suffix에서 어떤 타입을 사용할 것인지
  * \<context: component-scan : 자동으로 패키지를 찾을때
* Model : 결과물을 저장하는 곳
  * java.util.Map을 받아서 Map 키-값 형태로 저장
  * model.put을 사용하게되면 위와 같은 형태이지만 항상 이름을 지정해줘야함
  * model.addAttribute(Object) : 이렇게 사용하면 키값을 주지 않아도 됌 또한 연속해서 사용가능
* Controller
  * @Controller를 사용해서 해당 클래스가 컨트롤러임을 알림
  * @RequestMapping : 해당 url을 받으면 이 클래스를 실행
    * url만 적을수 있고, method만 적을 수 있고, url + method 둘다 적을 수 있음
  * return "뷰파일이름"
  * @RequestParam : get방식의 파라미터값 ?key=value\&key2=value2이렇게 넘어오는 파라미터를 자동으로 key/value를 분리해서 줌
* View
  * JSTL(JSP Standard Tag Library)
    * POM.xml에 jstl을 추가해줘야함
    * <%@taglib prefix="c" 는 core에 해당
    * <%@taglib prefix="fn"은 jsrtl의 함수에 해당

Spring Web Form

* Web Basics
  * Request Parameters : http request message
    * get : query string으로 넘어오게 되고
    * post : HTTP body부분에 담겨서 넘어오게 된다
* Data Binding
  * request parameter를 객체로 변경하는 2가지 방법
    * @RequestParam
    * form bean
      * url을 받는 @RequestMapping(value="함수")로 함수를 선언하고 그 함수의 인자로 객체를 넣어줌
        * 새로운 form bean이 생성됨
        * request를 넣어줌
        * 모델에 추가됨
* Data Validation
  * Hibernate Validator
    * validation의 api인 JSR-303을 강화해서 만듬
    * @NotNull, @Size, @Pattern, custom의 Annotation
    * pom.xml에 hibernate-validator를 추가
    * 모든 태그에 message를 넣어서 에러 발생시 메세지를 출력
    * 위의 데이터 바인딩시, 객체를 넣어준 인자앞에 @Valid를 넣어주면 자동적으로 객체를 검증하고 결과가 model에 들어감
    * 또한 그 인자 다음 인자로 BindingResult를 넣어주면 result에 대한 객체로 사용가능
* Data Buffering
  * 만약 데이터를 입력한 후에 에러나가면 없어지니까 입력했던 정보를 남기는 방법
  * 방법
    * JSP파일에서 <%@taglib prefix='sf' 선언
      * 스프링폼이라는 의미
      * \<sf:form
      * \<sf:input
      * \<sf:password
      * \<sf:checkbox
      * \<sf:error

Spring Security

* servlet filter로 request와 response를 intercept해줌
* pom.xml에 spring-security-core, spring-security-web, spring-security-config를 넣어줌
  * DelegatingFilterProxy를 사용해서 모든 request를 intercept한다
* web.xml에서 springSecurityFilterChain을 넣어주고 \<param-value에 security-context를 넣어줌
* security-context에서 authentication부분에서 password시 {noop}으로 인코딩
  * ```
    <security:http auto-config="true" use-expression="true">
        <security:intercept-url pattern="/secured/**" access="hasRole('~')">
    ```
  * hasRole이나 isAuthenticated이나 permitAll
* Login폼을 특별히 정해주지 않으면 /login을 누르면 스프링에서 제공해주는 기본적인 로그인폼을 제공해줌
  * 자신이 만든 login form을 사용하고 싶다면 security-context에서 이렇게 해주면 form-login요소가 기본 세팅을 덮어써준다
  * @RequestMapping(value="/login", method=RequestMethod.GET) annotation을 추가해준 함수의 return은 view로 return="login"
  * form에서 \<c:url value="/login"
* Authentication Fail => JSP파일에서 \<c:if test="${not empty errorMsg}"

Logging : 어딘가에 기록해두는 과정

* 장점
  * 디버그는 완벽히 app의 문맥을 따라가지 않고 따로 저장한다
  * 사람의 개입이 없다
  * 영구적으로 저장되서 나중에 연구가능
* 단점
  * 애플리케이션의 속도를 낮춤
  * 너무 많을수도있음
* Logback방식을 자주 사용한다
* SLF4J(Simple Logging Facade for Java) : facade패턴을 사용했음
  * facade 패턴 : 클라이언트가 직접 시스템에 접근하지 않고 api를 거쳐서 접근하는 방식
  * import org.slf4j.Logger, LoggerFactory, getLogger(클래스)로 사용한다.
  * 대신 하나밖에 사용할 수 없다.
* Logback -> pom.xml에서 logback-classic을 선언해주면
  * 자동으로 slf4j-api와 logback-core를 가져와준다.
* 만약 따로 설정파일을 지정해주지 않는다면 logback.xml 파일을 찾는다.
* Logback class
  * logback-core : Appender, Layout
  * logback-classic : Logger
    * logger의 단계 : trace -> debug -> info -> warn -> error
      * 레벨지정을 해주지 않는다면 가장 가까운 부모의 단계를 상속받고
      * root logger의 디폴트 값은 debug이다.
      * parameterized logging
      *   Object entry = new SomeObject();

          logger.단계("The entry is {}.", entry)
    * appenders : 어디에다가 로그 기록을 넣을 것인가?
      * higher in the hierarchy 자식은 부모, 루트의 지정장소 모두에 적게된다.
        * Console, File, Appender :
        * ```
           <appender name="STDOUT" class="~ConsoleAppender">
           or
           <appender name="FILE" class=="~FileAppender">
               <encoder>
                   <pattern> %d

           <root level="info">
               <appender-ref ref="STDOUT"/>
           </root>
           or
           <root level="info">
               <appender-ref ref="STDOUT"/>
               <appender-ref ref="FILE"/>
          ```

maven / pojo /di - singleton, prototype / selectSound() / @Valid / 주소쓰기-url / login , login?error, logout / @Repository / data object / ="offer" / \<prefix /
