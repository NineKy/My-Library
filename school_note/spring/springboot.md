# SpringBoot의 특징

SpringBoot는 SpringBoot을 만든 사람의 주관이 담겨져 있는 상태로 설정되어있다.

SpringBoot을 통해서 SpringBoot 프로젝트를 가능한 빠르게 생성하는 것이 가능하다

→ 이를 통해서 생산성은 높아지게 되었다.

SpringBoot에는 세가지 특징이 존재하고 있다.

1. 수동적인 설정들을 비약적으로 줄임\(auto configuration\)
2. 의존성 버전의 충돌을 해결\(dependency conflict\)
3. 내장서버를 가지고 있다.\(Embedded Server\)

#### Auto Configuration

기본적으로 SpringBoot에 대한 설정을 진행하기위해선 3가지 파일에 설정이 필요하다

1. web.xml
   1. dispatcher servlet
2. Servlet-context.xml
   1. component-scan
   2. view resolver
   3. resource
3. dao-context.xml
   1. data source
   2. session factory
   3. transaction manager

이런 설정들을 auto-configuration을 통해서 한번에 해결하는 것이 가능하다.

파일의 방식은 : properties\(props\)파일이거나 JAR lib을 사용한다.

* 여기서 JAR는 어떤 library가 classpath에 있는가를 확인

@SpringBootApplication

은 여러가지의 애노테이션을 조합해서 만든 애노테이션이다.

* @EnableAutoConfiguration : 자동으로 설정들을 완료해줌
* @ComponentScan : 하위 directory들까지 탐색해서 bean들을 등록해줌
* @Configuration : 이게 달려있는 클래스도 bean으로 등록해줌

이후 main함수 안에서 SpringApplication.run\(\)을 통해서 application context을 만들고 모든 bean들을 등록하고, 내장서버를 실행한다

만약 다른 package에 있는 bean을 등록하고 싶다면

@SpringBootApplication\(scanBasePackages={현재패키지, 스캔하고싶은패키지....}\) 이렇게 넣어줄 수 있다.

* @ComponentScan\(basePackage= {현재패키지, 스캔하고싶은 패키지...}\)로도 가능

@EnableAutoConfiguration

을 통해서 활성화를 시켜주고 활성화하게 된다면 다양한 component들을 자동으로 등록해준다

@EnableConfigurationProperties\(DataSourceProperties.class\)

원래 수동으로 설정할 속성들\(properties\)은 대부분 [application.properties](http://application.properties/) 라는 파일에 들어가게 된다.

이 애노테이션을 사용하게 되면 application.properties에 있는 설정값들을 매개변수로 넣어준 파일에 자동으로 바인딩 시켜준다

@Condition

을 통해서 여러 조건 걸어줄 수 있다

@ConditionOnClass\({DataSource.class, EmbeddedDatabaseType.class}\)

만약 어떤 클래스와 타입이 classpath에 존재한다면, 그때서야 auto configuration bean이 고려된다. = 해당 애노테이션이 있는 클래스 안에 @Bean이라고 등록되어있는 bean들이 고려된다

@ConditionalOnMissingBean

이 애노테이션이 달려있는 bean은 만약 이 bean이 container에 등록되어있지 않을때 등록하게 해주는 애노테이션이다

= 이 애노테이션이 container에 있느냐 없느냐? 없어? 그럼 등록 있어? 그럼 생략

#### Easy Dependency Management

→ 이것을 도와주기 위해서 SpringBoot Starters라는게 있다.

Dependencies in Spring MVC

* spring-webmvc
* jackson-databind
* hibernate-validator

적절한 버전들을 호환되게 해야한다 → 하지만 버전을 맞게 못할수도 있기 때문에

spring-boot-starter-

web

security

data-jpa

logging등이 있음

이렇게만하게되면 수동적으로 버전들을 찾고 그럴필요는 없다는 것 = 여러가지 Maven dependencies 들을 호환되는 버전으로 모아둔 것이다

위에 3가지 요소들을 굳이 수동으로 입력할필요없고 그냥 입력하면 된다.

만약 버전을 명시를 하게 된다면 자동으로 overriding이 이루어진다!!

#### Embedded Servlet Container Support

main메소드를 돌리게 되면 tomcat이 embedded container로써 실행이 된다

여기서 패키징 타입은 jar이다!!! - war가 아니다!!!

#### SpringBoot Actuator

애플리케이션을 모니터링하고 추적할 수 있게 해준다

→ 실제 service할때 중요

REST endpoints로 자동으로 넣어준다.

/beans은 현재 애플리케이션에 등록되어있는 모든 bean들을 확인할 수 있음

/mappings은 모든 url으로 매핑되어있는 URL을 한번에 확인할 수 있다.

기본 endpoint는 actuator이다.

/health와 /info을 디폴트로 노출되어있는 상태이다

= /actuator/health , /actuator/info

/health은 현재 애플리케이션의 상태를 UP, DOWN으로 표시해줌

/info는 애플리케이션에 대한 정보를 알려줌

* application.properties에다가 info.app. 밑에 name, version, 등 적어주면 된다

만약 모든 actuator를 노출시키고 싶다면 application.properties로 가서

management.endpoints.web.exposure.include=\*

management.endpoints.web.exposure.include=env.beans

사용하기 위해서는 POM.xml에 의존성을 추가해줘야한다.

→ spring-boot-starter-actuator

일단 maven이 설치되어 있지 않은 경우가 있다.

mvnw = mvn wrapper

mvnw이나 mvnw.cmd을 실행해주면 필요한 mvn들을 자동으로 설치

이후에는mvn clean package로 애플리케이션을 실행하면된다.

그리고 패키징은

java -jar target\hellospringboot-0.0.1-SNAPSHOT.jar

= mvn spring-boot:run

