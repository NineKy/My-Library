# SpringBoot 생성 방법

### Spring Initializr으로 생성하는 방법

[http://start.spring.io](http://start.spring.io/)

에서 SpringBoot application을 생성할 수 있다

초기설정

* Maven vs Gradle
* java vs kotlin vs groovy
* SpringBoot version
* metadata
* dependencies

SNAPSHOT이라는 건 아직 개발하고 있는 단계라는 뜻

위에서 만든 걸 다운받고, 압축을 풀어서 사용!!!

기본적인 구조는

/src/main/java → 앞으로 만든 자바 소스 코드들

/src/main/resource → properties, html, css, images등등

* static or public - 웹에 대한 것들\(html, css, js 등\)을 넣어줌
* templates - \(뷰\) template파일들을 넣어줌 \(thymeleaf, mustache, freemarker\)

/src/test/java → 테스팅 코드

/src/main/webapp은 jar파일로 생성할떄 절!!!!!대!!!!! 사용하지 마라

#### Project뜯어보기

pom.xml

* groupId, artifactId, version, packaging

pom.xml은 spring-boot-starter-parent을 상속받고 있고, spring-boot-starter-parent는 spring-boot-dependencies을 상속받고있다.

pom.xml → spring-boot-starter-parent → spring-boot-dependencies

spring-boot-starter-web을 사용하게 되면

1. 자동으로 DispatcherServlet이 모든 request들을 처리해준다. 즉, 알맞는 컨트롤러로 보내주게 된다.
2. 내장된 서버로써, 톰캣을 돌려준다.

spring-boot-starter-thymeleaf

build에 관련된거 → spring-boot-maven-plugin이 들어가있음

이건 Spring Boot에 관한 지원을 해주는 거임

1. jar나 war로 패키징해줌
   * Fat jar파일 : 이 파일자체만으로도 실행할 수 있도록 톰캣이나 라이브러리등을 다 들어있는걸로 만들어짐
   * maven-jar-plugin을 통해서 jar파일이 만들어진다 / maven-jar-plugin을 통해서 repackaging해서 또 하나의 fat jar 파일을 만들게 된다
   * hellospringboot-0.0.1-SNAPSHOT.jar\(4306mb\) 와 hellospringboot-0.0.1-SNAPSHOT.jar.original\(20496536mb\)크기의 fat jar파일이 만들어짐
2. spring boot application을 돌려준다

mvn package - phase을 가리킴

mvn spring-boot:run - phase을 가리키고 : goal을 줌

Spring Boot Maven Plugin : repackage, run, start, stop, ...

#### Application EntryPoint Class

WebApplicationType에는

* NONE → 내장 웹서버가 없음
* REACTIVE → 비동기 프로그래밍
* SERVLET → servlet에 기반한 웹 애플리케이션 / spring-boot-starter-web을 보고 자동으로 servlet으로 지정해줌

지정하고 싶다면 application.properties에서 spring.main.web-application-type=none 이렇게

만약에 값을 안넣어주면 app에서 자동으로 탐지해서 값을 넣어줌!

#### Spring Boot Dev Tools

코드가 업데이트 될때마다 자동으로 애플리케이션을 재실행해줌

pom.xml에 spring-boot-devtools 만 추가해주면된다

#### Spring MVC

controller 클래스에서 인자에 Model값을 주고 클래스 내부에서 model.addAttribute\(변수명, 내용\) 이렇게 해주고 해당 컨트롤러에서 리턴하는 thymeleaf에서는 ${변수명} 이렇게만해주면 자동으로 thymeleaf engine이 데이터값을 넘겨주게 된다.

#### Application Properties

기본적으로 [application.properties](http://application.properties/) 사용

* server.port=9000으로 포트번호 변경가능
* server.servlet.context-path=/helloSpringBoot으로 /말고 /helloSpringBoot으로 시작하게 됨
* logging.level.로거이름=로거 레벨
  * Logger logger = LoggerFactory.getLogger\(this.getClass\(\)\); 이렇게 선언해주고, logger.debug\("professor name: {}", professor\);이렇게 해주면, {}값에 변수값인 professor가 들어가서 console에 찍히게 된다
* 원하는이름.원하는 이름=데이터값\(app.professor=Namyun Kim\)
  * 컨트롤러에서 @Value\("${app.원하는이름}"\) 을 붙혀주면 붙힌 변수에 지정했던 데이터가 들어가게 된다
  * jdbc.driver=~, jdbc.url=~여러가지일때는 힘드니까 @ConfigurationProperties\(prefix="jdbc"\)이렇게 해주면 굳이 @Value\("${~}"\)이렇게 일일이 해줄 필요가 없어진다

