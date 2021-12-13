# 프로젝트 세팅 및 기본설정

gradle 프로젝트를 만드는데

could not find method compile() for arguments

이러한 에러가 나오길래 찾아봤더니, 기존에 build.gradle을 사용하는 것보다 현재 사용하는 버전이 더 높아서 그렇다고한다..!

기존의 gradle에서는 compile, testCompile을 사용했지만

현재 본인이 사용하는 7.1버전에서는 implementation, testImplementation 을 사용한다는점!

build.gradle

```yaml
configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
    all{
        exclude group: "org.springframework.boot", module : "spring-boot-starter-logging"
    }
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jdbc'
    implementation 'org.springframework.boot:spring-boot-starter-actuator'
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
    implementation ('org.springframework.boot:spring-boot-starter-web'){
        exclude module : 'spring-boot-starter-tomcat'
    }
    implementation 'org.springframework.boot:spring-boot-starter-undertow'
    compileOnly 'org.projectlombok:lombok'
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
    runtimeOnly 'com.h2database:h2'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

test {
    useJUnitPlatform()
}
```

톰캣 서버가 아니라 undertow를 사용

* undertow은 blocking, non-blocking이 모두 사용되도록 설계된 웹 서버
* 대규모 트래픽으로 부터 톰캣보다 안전하다고 함
* 그래서 회사에서도 항상 undertow를 사용

log4j2를 사용하기 위해서

* exclude를 통해서 기존에 제공하고 있는 spring-boot-starter-logging를 제외하고
* log4j2를 넣어줌

21.12.13 - log4j2 라이브러리에서 엄청난 장애가 발생해버렸다..!&#x20;

log4j2의 기능중에 어떤 버그가 발생하게 되면 특정 작업을 트리거되게 할 수 있는 기능이 있다. 이 버그를 찾낸 사람들은 마인크래프트를 하다가 찾아냈다고 하던데... 이것이 겜돌이들의 힘..? 쨋든 트리거 기능을 악용해서 클라이언트에서 서버로 공격이 가능하고 서버에서도 서버로 공격이 가능하다..! 트리거 기능을 활용해서 컴터를 끄거나 이상한 프로그램을 실행시키는 것이 가능하다.&#x20;

{% embed url="https://spring.io/blog/2021/12/10/log4j2-vulnerability-and-spring-boot" %}
스프링에서 공식적으로 이야기한 버그와 해결책
{% endembed %}





실습의 진행을 도커를 사용해서 db를 띄워볼까? 일단 현재는 h2로 진행하기 위해서 application.properties에 h2에 대한 값들을 넣어주자 그리고 추가적으로 필요한 세팅들 추가

```yaml
spring.h2.console.enabled=true
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

spring.jpa.hibernate.ddl-auto=create-drop
#SQL보기
spring.jpa.properties.hibernate.show_sql=true
#SQL이쁘게 보기
spring.jpa.properties.hibernate.formate_sql=true
#추가적인 주석 추가
spring.jpa.properties.hibernate.use_sql_comments=true
#?에 어떤 값이 들어갔는지 보기
logging.level.org.hibernate.type.descriptor.sql=trace
```

일단 이렇게만 해두고 h2를 실행하고 테이블을 만들자 - 아근데 이건 mysql같은데.. 일단 회사에서도 오라클을 쓰니까 오라클을 사용하자

```sql
create table member(
 ID VARCHAR(255) NOT NULL,
 NAME VARCHAR(255),
 AGE INTEGER NOT NULL,
 PRIMARY KEY (ID)
)
```

```sql
--오라클 버전 테이블 생성
CREATE TABLE MEMBER(
	ID VARCHAR(255) NOT NULL,
	NAME VARCHAR(255),
	AGE NUMBER(3) NOT NULL,
	CONSTRAINT ID PRIMARY KEY
)
```

이렇게 하고 값을 매핑하기 위해서 클래스를 생성

```java
package SimpleDBObject;

import lombok.Data;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.Table;

@Data
@Entity
@Table(name="MEMBER")
public class Member {
    @Id
    private String id;
    @Column
    private String username;
    private String age;
}
```

많이 해봤던 것들이다

@Data을 사용해서 getter와 setter등 필요한 롬복기능을 사용

@Entity를 사용해서 해당 클래스를 DB와 매핑할 것이라고 JPA에게 알려줌

@Table을 사용해서 엔티티 클래스에 매핑할 테이블의 정보를 알려준다

* name 속성을 사용해서 해당 엔티티를 MEMBER 테이블에 매핑
* 만약에 딱히 @Table을 붙히지 않는다면 알아서 클래스의 이름과 같은 테이블을 찾아서 매핑해줌

@Id를 사용해서 해당 클래스에서 기본 키에 매핑시켜준다

* @Entity만 붙히면 자동으로 이게 없으면 빨간줄이 나온다!

@Column을 사용해서 각 필드들을 컬럼에 매핑해준다.

* 만약에 필드와 디비의 컬럼 이름이 같다면 굳이 선언해주지 않아도 알아서 매핑됨!

추가적으로

@Transient을 사용해서 굳이 엔티티의 컬럼에 매핑하지 않겠다는 표시를 해줄 수 있음

엔티티 매니저 사용

엔티티매니저란? → 특정한 작업을 위해서 DB에 접속하는 놈이고 엔티티를 DB에 조회, 등록, 수정, 삭제

```java
package SimpleDBObject;

import javax.persistence.*;

public class JPAMain {
    public static void main(String[] args) {
        //엔티티 매니저 팩토리 생성
        EntityManagerFactory entityManagerFactory = Persistence.createEntityManagerFactory("jpabook");
        //엔티티 매니저 생성
        EntityManager entityManager = entityManagerFactory.createEntityManager();
        //트랜잭션 가져오기
        EntityTransaction tx = entityManager.getTransaction();
        
        try{
            tx.begin();
            logic(entityManager);
            tx.commit();
        } catch (Exception e){ 
            tx.rollback();
        } finally {
						//엔티티 매니저 종료
            entityManager.close();
        }
        entityManagerFactory.close();
    }
    
    public static void logic(EntityManager em){
				String id = "id";
        Member member = new Member();
        member.setId(id);
        member.setUsername("더미원");
        member.setAge("22");

        //등록
        em.persist(member);

        //수정
        member.setAge("25");

        //조회
        Member found = em.find(Member.class, id);
        System.out.println(found.toString());

        //다수조회
        List<Member> list = em.createQuery("select m from Member m", Member.class)
                .getResultList();
        System.out.println("list.size : "+list.size());

        //삭제
        em.remove(member);
    }
}
```

엔티티 매니저 팩토리

* 엔티티 매니저 팩토리는 JPA를 동작시키기 위한 기반 객체를 만들고, JPA 구현체에 따라서 connection pool도 생성하기 때문에 아주 무겁다..!
* 따라서 엔티티 매니저 팩토리는 애플리케이션 전체에서 한 번만 생성하고 공유해서 사용해야 한다 = 멀티 스레드에서 접근이 가능해야한다

엔티티 매니저

* JPA의 대부분의 기능을 제공해주는 놈
* 엔티티를 조회, 등록, 수정, 삭제가 가능하다
* 엔티티 매니저는 db connection pool과 밀접한 관계가 있기 때문에 스레드 간에 공유하거나 재사용되면 안된다.
* 다음 장에서 조금 더 자세하게 배울 예정이고 엔티티 매니저는 애초에 웹 앱을 사용하면서 하나가 만들어지기 때문에 위에 코드는 스프링부트에서 실행되지는 않는다. 만약 스프링부트에서 엔티티 매니저를 사용하고 싶다면 @Autowired 키워드를 사용해서 의존성을 주입 받아서 사용하는 방법이 있다.

JPQL : SQL을 추상화한 객체지향 쿼리 언어이다. 이 언어는 SQL을 추상화 했기 때문에 SQL과 문법이 거의 유사해서 어렵지 않게 사용할 수 있다.

* JPQL은 엔티티 객체를 대상으로 쿼리를 한다(자바 코드를 대상으로 쿼리를 적용한다는 뜻)
  * JPQL은 엔티티 객체를 대상으로 하기 때문에 디비 테이블을 모른다.
* SQL은 디비를 직접적으로 쿼리한다.
