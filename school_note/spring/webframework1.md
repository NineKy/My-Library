---
description: 스프링MVC에 관한 것들을 적어두었음
---

# Webframework1



hibernate는 ORM\(Object Relational Mapping\)을 도와주기 위한 solution중 하나이다

hibernate을 통해서 jdbc를 접속하게되는데

→spring에서 hibernate api를 통해서 접속

→hibernate에서 jdbc api를 통해서 접속

→jdbc에서는 원하는 값을 Resultset형식으로 리턴해주고

→hibernate에서는 그 Resultset형식을 domain object형식으로 사용자에게 건내준다

hibernate를 사용하기 위해 필요한건

* configuration
* sessionFactory
* mapping

Configuration은 dao-context.xml파일에서 지정해주거나, hibernate.cfg.xml에서 지정해주면됨

세부내용

* dataSource → 연결된 db에 관한 정보들을 넣어줌
* sessionFactory
  * 위에 빈으로 설정해준 dataSource
  * packagesToScan\(model\)
  * hibernate의 설정
    * &lt;prop key="hibernate.show\_sql"&gt;true
    * &lt;prop key="hbm2ddl.auto"&gt;create
* transcationManager\(위에서 설정한 sessionFactory\)
* component-scan\(dao\)

SessionFactory

→hibernate에서는 session을 만들어서 db와의 데이터를 주고 받는다

선언하는법

1. SessionFactory를 @Autowired로 dependency injection을 통해서 주입받는다
2. sessionFactory = new Configuration\(\).configure\(\).buildSessionFactory\(\);
3. Session session = sessionFactory.openSession\(\);
4. Transaction tx = session.beginTransaction\(\);
5. session밑의 get\(원하는타입.class, save한 serializational한 값\), saveOrUpdate\(\), delete\(\), flush\(\), close\(\), createQuery\(\)
6. tx.commit\(\);
7. session.close\(\);
8. sessionFactory.close\(\);

이렇게 단계를 거쳐서 접근한다

하지만 만약에 @Transactional이라는 주석을 사용하게되면

굳이 sessionFactory를 위처럼 객체를 만들고, transaction을 만들고 openSession, beginTransaction으로 시작안하고 commit도 안해도 된다

그냥 Session = sessionFactory.getCurrentSession\(\)을 이용하게되면 현재의 session을 가져오게되고 만약 session이 없다면 새로운 session을 만들어준다

그러면 @Transactional이라는 주석을 붙히면 필요한건 sessionFactory.getCurrentSession과 session의 메소드들\(get, save, saveOrUpdate, delete, close, flush, createQuery\)만이 남게 된다

* createQuery 사용

createQuery의 인자로 들어가는것은 HQL\(Hibernate Query Language\)과 해당 클래스이다

select을 수행시 .getResultList\(\) 을 통해서 확인할 수 있고

update을 수행시 .executeUpdate\(\) 을 통해서 db에 적용시킬 수 있다

Session Object States

* Transient
* Detached
* Persistent

JPA\(Java Persistance Api\)

Mapping

cascade = CascadeType

* ALL, PERSIST, MERGE, REMOVE, REFRESH

@Repository @Transaction

@Entity @Table\(name="~"\)

@Id @GenerateValue\(strategy.GenerationType.IDENTITY .AUTO .TABLE\) @Column\(name="~"\)

@Transient

@ManyToOne 단방향시

one쪽에는 딱히 해줄껀 없고

many쪽에는 이렇게

@ManyToOne\(cascade = CascadeType.ALL\)

@JoinColumn\(name="~"\)

2개를 넣어줌

@ManyToOne 양방향시

one쪽에는

@OneToMany\(mappedBy="many측에서의 변수", cascade=CascadeType.ALL\)

이렇게 넣어주고

many쪽에는

@ManyToOne

@JoinColumn\(name="~"\)이렇게만 넣어준다

@OneToOne 단방향시

사용되는쪽은 할거없고

사용하는쪽에서

@OneToOne\(optional=false, cascade=CascadeType.ALL\)

@JoinColumn\(unique=true, name="~"\)

이렇게 넣어준다

@OneToOne 양방향시

사용되는쪽에서

@OneToOne\(mappedBy="사용하는측에서의 변수이름", cascade=CascadeType.ALL\)

이렇게 넣어주고

사용하는쪽에서는

@OneToOne\(optional=false\)

@JoinColumn\(unique=true, name="~"\)

이렇게만 넣어준다

@ManyToMany 사용시

사용되는쪽에서는 할거 없고

사용하는쪽에서

@ManyToMany\(cascade = CascadeType.ALL\)

@JoinTable\(name="~", joinColumns={@JoinColumn\(name="~"\)}, inverseJoinColumns={@JoinColumn\(name="~"\)}

@OneToMany\(mappedBy="밑에서사용하는변수" @ManyToOne\(cascade=CascadeType.ALL, fetch=FetchType.EAGER\) @OneToOne\(optional=false, cascade=CascadeType.ALL\) @JoinColumn\(unique=true, name="~"\) @ManyToMany\(cascade=CascadeType.ALL\) @JoinTable\(name="~", joinColumn={@JoinColumn\(name="~"\)}, inverseJoinColumn={@JoinColumn\(name="~"\)

Hibernate와 SpringFramework을 연동하는 방법

1. Maven Dependency

   hibernate-core, spring-orm 두 태그가 들어가야함

2. Spring Bean Configuration → datasource, sessionfactory, transactionmanager

   hibernate.cfg.xml이나 dao-context.xml에 저장

   datasource요소들

   1. driverClassName
   2. url
   3. username
   4. password

   sessionFactory요소들

   1. datasource
   2. packagesToScan
   3. hibernateProperties
      1. show\_sql ⇒ true
      2. [hbm2ddl.auto](http://hbm2ddl.auto/) ⇒ create나 update
      3. hibernate.dialect

   transactionmanager

   1. transaction-manager="transactionManager"
   2. sessionFactory

   DAO Bean

   component-scan : base-package=dao

3. entity bean
4. dao layer

REST = REpresentational Status Transfer

* url, id값으로 resource를 분류한다
* resource의 표현은 xml, json, text등 으로 표현할 수 있다

RESTful Web Service는 decupling을해줌, client와 server의 분리한다. 그리고 platform agnostic, 즉 client의 platform에 상관없이 같은 것을 제공해준다는 의미

RESTful Web Service의 중요한 요소 3가지는

* method
* message
* resource

HTTP Request Message

* method + requestURL + httpversion
* header
* blank
* body

HTTP Response Message

* httpversion + status code + phrase
* header
* blank
* body

Addressing

⇒ resourceType/resourceId

* 복수형을 사용하라
* 공백쓰지말고 \_ 써라
* 소문자쓰라
* 안쓰는 url은 사용중인 url로 redirection시켜라
* url에 http method가 들어가면 안좋음

METHOD

* GET, POST, PUT, DELETE
* OPTION ⇒ 어떠한 http method을 유지할것인가
* HEAD ⇒ body는 없이 header만 보낼때

CACHING

* public
* private
* no-store
* max-age
* must-revalidate

ETag ⇒ resource의 version값으로, 이 값으로 변경사항이 있는지 확인 / 304 Not Modified

Controller → 객체를 return한다

객체를 JSON화 시키는 것은 Serialization 이라고 하고, 이것은 server에서 client로 값을 response해줄때 컨트롤러에서 리턴하는 객체를 json화 시켜서 전송해준다

JSON을 객체화 시키는 것은 Deserializaion이라고하고, 이것은 client가 server로 json을 전송할때 server에서 deserialization이 이루어진다

⇒이것은 jackson-databind 라는 library를 통해서 사용할 수 있는 기능임

@ResponseBody는 컨트롤러에서 객체를 리턴해줄 때, http의 body부분에 그 객체를 넣어주는 개념이다, 클래스에 사용하고, 위치는 접근지정자 다음에 적어주면 된다

@RestController = @Controller + @ResponseBody

@RequestBody는 client에서 보낸 http request의 body에 있는 내용을 객체로 바인딩시켜주는 단계

@PathVariable\(value="RequestMapping과 같은 url매핑단계에서의 변수가 들어간다"\) 타입 변수

이렇게 사용하면 url에서의 변수값을 이 주석뒤의 변수에 집어넣는 것이 가능

ResponseEntity&lt;타입&gt;

여기에는 http reponse의 내용이 들어가게 된다

* status code
* header
* body

Spring Configuration

1. XML based configuration
2. Annotation based configuration
3. Java based configuration

@Configuration

이건 bean을 정의하는 메서드임

이걸 선언한 함수안에서 @Bean 주석을 사용해서 bean을 만들어냄

@Component의 강화된 버전이기 때문에

이걸 선언한 함수도 bean이 됨

왜 SpringFramework를 사용하냐?

1. 객체들간의 의존성을 줄이기 위해서 → Dependency Injection을 사용해서 의존성을 줄인다

   객체를 다른 클래스에서 사용하기 위해서는 new키워드를 사용해서 쓰는데 이건 의존성이 너무 높다

2. Plumbing, BollerPlate 코드를 줄인다 ⇒ 필요하지만, businesslogic에는 쓸모없는 코드이기 떄문에 spring에서 해줌으로써 신경을 안써도 됨, 필요하면 abstraction이용해서 추가하면됨
3. 다른 framework와 통합이 쉬움 ⇒ 서드파티의 framework들을 사용하기가 편리하다

