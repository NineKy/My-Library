# Day 16

#### ![](../.gitbook/assets/\_2021-02-02\_\_12.23.24.png)외부 Security 파일 등록하기 <a href="2aa1f4b6-d1f8-4418-b4d8-9341461f7736" id="2aa1f4b6-d1f8-4418-b4d8-9341461f7736"></a>

ClientRegistrationRepository를 생성하면 clientId와 clientSecret가 필수인데 로컬에서는 application-oauth.properties가 있어서 문제가 없었지만

현재는 EC2에서 진행중이면서 github에서 다운로드 받은 프로젝트로 하는데 로컬에서 git에 올릴 때 application-oauth.properties는 .gitignore로 제외대상으로 빼두었음

애플리케이션을 실행하기 위해 공개된 저장소에 clientId와 ClientSecret을 올릴수는 없으니 서버에서 직접 이 설정들을 가지고 있도록 해야함

app디렉토리에 properties 파일을 생성

vim /home/ec2-user/app/application-aouth.properties

로 파일을 생성, 연다음에 로컬에서의 [application-aouth.properties](http://application-aouth.properties) 파일을 그대로 복사 붙혀넣기를 하고 저장종료를 한다음

방금 서버에서 생성한 application-aouth.properties을 쓰도록 deploy.sh를 수정

이것을 추가

```
nohup java -jar \
        -Dspring.config.location=classpath:/application.
properties,/home/ec2-user/app/application-oauth.properties \
        $REPOSITORY/$JAR_NAME 2>&1 &
```

* \-Dspring.config.location
  * 스프링 설정 파일 위치를 지정
  * 기본 옵션들을 담고 있는 application.properties와 OAuth 설정들을 담고 있는 application-oauth.properties의 위치를 지정
  * classpath가 붙으면 jar 안에 있는 resources 디렉토리를 기준으로 경로가 생성됨
  * application-oauth.properties은 절대 경로를 사용, 외부에 파일이 있기 때문

#### 스프링 부트 프로젝트로 RDS 접근하기 <a href="0842d12c-d900-41c4-9fdb-8ef1c2a166bd" id="0842d12c-d900-41c4-9fdb-8ef1c2a166bd"></a>

RDS는 MariaDB를 상ㅇ중인데 여기서 스프링부트 프로젝트를 실행하기 위해서는 몇 가지 작업이 필요

* 테이블 생성 : H2에서 자동으로 생성해주던 테이블들을 MariaDB에선 직접 쿼리를 이용해 생성
* 프로젝트 설정 : 자바 프로젝트가 MariaDB에 접근하려면 데이터베이스 드라이버가 필요하기 때문에 MariaDB에서 사용 가능한 드라이버를 프로젝트에 추가
* EC2(리눅스) 설정 : 데이터베이스의 접속 정보는 중요하게 보호해야할 정보이다. 만약 공개되면 외부에서 데이터를 모두 가져갈 수 있기 때문이다. 프로젝트안에 접속 정보를 가지고 있다면 깃허브와 같이 오픈된 공간에선 누구나 해킹할 위험이 있다. 그래서 EC2 서버 내부에서 접속 정보를 관리하도록 설정

RDS테이블을 생성 : 여기서는 JPA가 사용될 엔티티 테이블과 스프링 세션이 사용될 테이블 2가지 종류를 생성

JPA가 사용할 테이블은 테스트 코드 수행시, 로그로 생성되는 쿼리, schema-mysql.sql파일에서의 세션테이블 쿼리를 복사해서 RDS에 반영

```
create table posts (id bigint not null auto_increment, created_date datetime, modified_date datetime, author varchar(255), content TEXT not null, title varchar(500) not null, primary key (id)) engine=InnoDB;
create table user (id bigint not null auto_increment, created_date datetime, modified_date datetime, email varchar(255) not null, name varchar(255) not null, picture varchar(255), role varchar(255) not null, primary key (id)) engine=InnoDB;


CREATE TABLE SPRING_SESSION (
                                PRIMARY_ID CHAR(36) NOT NULL,
                                SESSION_ID CHAR(36) NOT NULL,
                                CREATION_TIME BIGINT NOT NULL,
                                LAST_ACCESS_TIME BIGINT NOT NULL,
                                MAX_INACTIVE_INTERVAL INT NOT NULL,
                                EXPIRY_TIME BIGINT NOT NULL,
                                PRINCIPAL_NAME VARCHAR(100),
                                CONSTRAINT SPRING_SESSION_PK PRIMARY KEY (PRIMARY_ID)
) ENGINE=InnoDB ROW_FORMAT=DYNAMIC;

CREATE UNIQUE INDEX SPRING_SESSION_IX1 ON SPRING_SESSION (SESSION_ID);
CREATE INDEX SPRING_SESSION_IX2 ON SPRING_SESSION (EXPIRY_TIME);
CREATE INDEX SPRING_SESSION_IX3 ON SPRING_SESSION (PRINCIPAL_NAME);

CREATE TABLE SPRING_SESSION_ATTRIBUTES (
                                           SESSION_PRIMARY_ID CHAR(36) NOT NULL,
                                           ATTRIBUTE_NAME VARCHAR(200) NOT NULL,
                                           ATTRIBUTE_BYTES BLOB NOT NULL,
                                           CONSTRAINT SPRING_SESSION_ATTRIBUTES_PK PRIMARY KEY (SESSION_PRIMARY_ID, ATTRIBUTE_NAME),
                                           CONSTRAINT SPRING_SESSION_ATTRIBUTES_FK FOREIGN KEY (SESSION_PRIMARY_ID) REFERENCES SPRING_SESSION(PRIMARY_ID) ON DELETE CASCADE
) ENGINE=InnoDB ROW_FORMAT=DYNAMIC;
```

#### 프로젝트 설정 <a href="7a538e4e-df6c-41b3-ab8e-03656a24dc96" id="7a538e4e-df6c-41b3-ab8e-03656a24dc96"></a>

먼저 MariaDB드라이버를 build.gradle에 등록

```
compile('org.mariadb.jdbc:mariadb-java-client')
```

그리고 서버에 구동될 환경을 하나 구성한다.

src/main/resources에 application-real.properties파일을 추가

* application-real.properties로 파일을 만들면 profile=real인 환경이 구성된다
* 실제로 운영될 환경이기 때문에 보안/로그상 이슈가 될 만한 설정들을 모두 제거하며 RDS 환경 profile 설정이 추가

```
spring.profiles.include=oauth,real-db
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
spring.session.store-type=jdbc
```

이렇게 하고 푸쉬

#### EC2 설정 <a href="27627b10-6216-4ef8-9844-ed0dd034a33b" id="27627b10-6216-4ef8-9844-ed0dd034a33b"></a>

OAuth와 마찬가지로 RDS 접속 정보도 보호해야할 정보이기 때문에 EC2 서버에 직접 설정 파일을 둔다

app 디렉토리에 [application-real-db.properties](http://application-real-db.properties) 파일을 생성

```
spring.jpa.hibernate.ddl-auto=none
spring.datasource.url=jdbc:mariadb://springboot-book.cjkbpw0rm3r3.ap-northeast-2.rds.amazonaws.com:3306/springboot-bo
ok
spring.datasource.username=kyu9
spring.datasource.password=shim0329
spring.datasource.driver-class-name=org.mariadb.jdbc.Driver
```

* spring.jpa.hibernate.ddl-auto=none
  * JPA로 테이블이 자동 생성되는 옵션을 None(생성하지 않음)으로 지정
  * RDS에서는 실제로 운영으로 사용될 테이블이기 때문에 절대 스프링 부트에서 새로 만들지 않도록 해야함
  * 이 옵션을 하지 않으면 테이블이 모두 새로 생성될 수 있음
  * 주의해야함

마지막으로 deploy.sh가 real profile을 사용할 수 있도록 코드추가

```
nohup java -jar \
        -Dspring.config.location=classpath:/application.properties,/home/ec2-user/application-oauth.properties,
/home/ec2-user/app/application-real-db.properties,classpath:/application-real.properties \
        -Dspring.profiles.active=real \
        $REPOSITORY/$JAR_NAME 2>&1 &
```

* Dspring.profiles.active=real
  * application-real.properties를 활성화
  * application-real.properties의 spring.profiles.include=oauth,real-db 옵션 때문에 real-db 역시 함께 활성화 대상에 포함

왜자꾸

![](<../.gitbook/assets/\_2021-02-02\_\_12.23.24 (1).png>)

이렇게 뜨면서 에러가 나는걸까...

이렇게 설정하고 ./deploy.sh 로 실행하고 나서 nohup.out 파일을 열어
