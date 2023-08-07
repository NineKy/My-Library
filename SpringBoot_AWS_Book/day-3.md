# Day 3

## Day3

## 스프링 부트에서 JPA로 데이터 베이스를 다루기

#### 👉JPA의 등장 배경

JPA라는 자바 표준 ORM(Object Relational Mapping)기술

객체를 관계형 데이터베이스에서 관리하는 것

단순 SQL문을 사용히 문제

* SQL문은 단순한 반복 작업이 되고 , 이는 피할 수 없다
* 패러다임 불일치
  * 관계형 데이터베이스는 어떻게 데이터를 저장할지에 초첨이 맞춰진기술
  * 객체지향 프로그래밍 언어는 메세지를 기반으로 가능과 속성을 한 곳에서 관리하는 기술
  * 객체를 관계형 데이터베이스에 저장하려고 하니 문제가 발생하고 이를 패러다임 불일치라고 함

JPA는 중간에서 패러다임을 일치시켜주기 위한 기술

\= 개발자는 객체지향적으로 프로그래밍을 하고, SQL에 종속적인 개발을 하지 않아도 된다

#### 👉Spring Data JPA란?

JPA는 인터페이스로서 자바 표준 명세서이다. 인터페이스이기 때문에 사용하기 위해서는 구현체가 필요함( ex - Hibernate, Eclipse 등) ⇒ 하지만 Spring에서 JPA를 사용할 때는 이 구현체를 직접 다루지 않음

* JPA ← Hibernate ← Spring Data JPA

Spring Data JPA의 등장 배경

* 구현체 교체의 용이성
  * \=Hibernate 외에 다른 구현체로 쉽게 교체하기 위함
  * 새로운 구현체가 나와서 교체할 필요가 있을 때 쉽게 교체가능
* 저장소 교체의 용이성
  * 관계형 데이터베이스 외에 다른 저장소로 쉽게 교체하기 위함
  * 만약 mongoDB로 교체할 필요가 있다면 의존성만 교체해주면 된다

JPA를 사용하지 못하는 가장 큰 이유는 어려워서 : 객체 지향 프로그래밍과 관계형 데이터베이스를 잘 이해해야함



## 앞으로 만들 게시판의 요구사항 분석

#### 게시판 기능

* 게시판 조회
* 게시글 등록
* 게시글 수정
* 게시글 삭제

#### 회원기능

* 구글/네이버 로그인
* 로그인한 사용자 글 작성 권한
* 본인 작성 글에 대한 권한 관리



## 프로젝트에 Spring Data JPA 적용하기

build.gradle

```java
dependencies {
    compile('org.springframework.boot:spring-boot-starter-web')
    annotationProcessor("org.projectlombok:lombok")
    compileOnly("org.projectlombok:lombok")
    compile('org.springframework.boot:spring-boot-starter-data-jpa')
    compile('com.h2database:h2')
    testCompile('org.springframework.boot:spring-boot-starter-test')
}
```

spring-boot-starter-data-jpa

* 스프링 부트용 Spring Data JPA 추상화 라이브러리
* 스프링 부트 버전에 맞춰 자동으로 JPA관련 라이브러리들의 버전을 관리

h2

* 인메모리 관계형 데이터베이스
* 별도의 설치가 필요 없이 프로젝트 의존성만으로 관리 가능
* 메모리에서 실행되기 때문에 앱 재시작시 초기화(테스트 용도로 많이 사용)

src/main/java/com/kyu/book/springboot에 domain패키지 생성

* 도메인 = sw에 대한 요구사항 혹은 문제 영역

domain내부에 게시물(post)를 위한 Post.class생성

src/main/java/com/kyu/book/springboot/domain/posts/Posts.class

```java
package com.kyu.book.springboot.domain.posts;

import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

import javax.persistence.*;

@Getter
@NoArgsConstructor
@Entity
public class Posts {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(length=500, nullable = false)
    private String title;

    @Column(columnDefinition = "TEXT", nullable = false)
    private String content;

    private String author;

    @Builder
    public Posts(String title, String content, String author){
        this.title = title;
        this.content = content;
        this.author = author;
    }
}
```

@Entity

* 테이블과 링크(데이터베이스와 연결)될 클래스임을 나타냄
* [SalesManager.java](http://salesmanager.java) → sales\_manager table
* 이런 식으로 db의 이름을 짓는다

@Id

* 해당 테이블의 Primary Key 필드를 나타냅니다

@GeneratedValue

* Primary Key의 생성 규칙을 나타냄
* 스프링 부트 2.0에서는 GenerationType.IDENTITY 옵션을 추가해야만 auto\_increment가 됨

@Column

* 테이블의 칼럼을 나타내며 굳이 선언하지 않더라도 해당 클래스의 필드는 모두 칼럼이 됨
* 사용하는 이유는, 기본값 외에 추가로 변경이 필요한 옵션이 있으면 사용합니다
* 문자열의 경우에는 varchar(255)가 기본값인데, 사이즈를 500으로 늘리거나 / 타입을 변경하고 싶을때 사용

@NoArgsConstructor

* 기본 생성자 자동으로 추가
* public Posts(){}와 같은 효과

@Getter

* 클래스 내 모든 필드의 Getter 메소드를 자동 생성

@Builder

* 해당 클래스의 빌더 패턴 클래스를 생성
* 생성자 상단에서 선언 시 생성자에 포함된 필드만 빌더에 포함

\*Entity의 primarykey는 Long타입의 auto\_increment를 추천

\*Entity에는 절대로 Setter 메소드를 만들지 않는다

* 인스턴스 값들이 언제 어디서 변환되는지 코드상으로 명확하게 구분할 수 없기 때문에 차후 기능 변경시 문제가 생김
* 해당 필드의 값 변경이 필요하면 명확히 그 목적과 의도를 나타낼 수 있는 메소드를 추가해야함

#### 기본적인 구조

생성자를 통해 최종 값을 채운 후 DB에 삽입한다. 또한 값 변경이 필요한 경우 해당 이벤트에 맞는 public 메소드를 호출하여 변경

* 여기서는 @Builder를 통해 제공되는 빌더 클래스를 사용, 빌더 패턴을 적극적으로 사용함

#### 👉Posts클래스로 DB에 접근하기 위한 JpaRepository를 생성

src/main/java/com/kyu/book/springboot/domain/posts/PostsRepository.interface

```java
package com.kyu.book.springboot.domain.posts;

import org.springframework.data.jpa.repository.JpaRepository;

public interface PostsRepository extends JpaRepository<Posts, Long> {
}
```

JPA에서는 Repository라고 부르며 인터페이스로 생성

JpaRepository\<Entity클래스, primary key 타입>을 상속하게 되면 기본적인 CRUD 메소드가 자동으로 생성

@Repository를 추가할 필요 없음 → Entity클래스와 기본 Entity Repository는 함께 위치

#### 👉Spring Data JPA 테스트 코드 작성하기

src/test/java/com/kyu/book/springboot/domain/posts/PostRepositoryTest.class

```java
package com.kyu.book.springboot.web.domain.posts;

import com.kyu.book.springboot.domain.posts.Posts;
import com.kyu.book.springboot.domain.posts.PostsRepository;
import org.junit.After;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import java.util.List;

import static org.assertj.core.api.AssertionsForClassTypes.assertThat;

@RunWith(SpringRunner.class)
@SpringBootTest
public class PostsRepositoryTest {
    @Autowired
    PostsRepository postsRepository;

    @After
    public void cleanup(){
        postsRepository.deleteAll();
    }

    @Test
    public void postsSave_Load(){
        String title = "테스트 게시글";
        String content = "테스트 본문";

        postsRepository.save(Posts.builder()
						.title(title)
						.content(content)
						.author("kyudo97@naver.com").build());

        List<Posts> postsList = postsRepository.findAll();

        Posts posts = postsList.get(0);
        assertThat(posts.getTitle()).isEqualTo(title);
        assertThat(posts.getContent()).isEqualTo(content);
    }
}
```

@After

* Junit에서 단위 테스트가 끝날 때마다 수행되는 메소드를 지정
* 보통은 배포 전 전체 테스트를 수행할 때 테스트 간 데이터 침범을 막기 위해 사용
* 여러 테스트가 동시에 수행되면 테스트용 db인 H2에 데이터가 그대로 남아 있어서 다음 테스트 실행 시 테스트 실패할 수 있음

postsRepository.save

* 테이블 posts에 insert/update 쿼리를 실행
* id값이 있다면 update가 / 없다면 insert 쿼리가 실행

postsRepository.finaAll

* 테이블 posts에 있는 모든 데이터를 조회해오는 메소드

#### 여기서 실제로 실행된 쿼리는 어떤 형태일까??

⇒ 한 줄의 코드로 설정할 수 있음

src/main/resources 디렉토리 아래에

resource파일 형태, application이름의 파일을 만든다

src/main/resources/application.properties

```java
spring.jpa.show_sql=true
```

이렇게 옵션을 추가해주면

로그에서 쿼리를 확인 할 수 있음

\+MySQL버전으로 변경하기 위해서 추가

```java
spring.jpa.show_sql=true
spring.jpa.properties.hibernate.dialect=
org.hibernate.dialect.MySQL5InnoDBDialect
```
