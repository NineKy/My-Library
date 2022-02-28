# Day 5

## Day5

## JPA Auditing으로 생성시간/수정시간 자동화하기

보통 Entity에는 해당 데이터의 생성시간과 수정시간을 포함하게 됨 ⇒ 유지보수시 중요한 정보이기 때문에 필요하지만 일일이 코드를 추가하기에는 귀찮고 코드가 길어지기 때문에 JPA Auditing을 사용함

### 👉LocalDate를 사용

java8부터 기본 날짜 타입인 Date의 문제점을 보안한 타입

Entity들의 createdDate, modifiedDate를 자동으로 관리하는 역할

src/main/java/com/kyu/book/springboot/domain/BaseTimeEntity.class

```java
package com.kyu.book.springboot.domain;

import lombok.Getter;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.EntityListeners;
import javax.persistence.MappedSuperclass;
import java.time.LocalDateTime;

@Getter
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public class BaseTimeEntity {
    @CreatedDate
    private LocalDateTime createdDate;
    
    @LastModifiedDate
    private LocalDateTime modifiedDate;
}
```

@MappedSuperclass

* JPA Entity 클래스들이 BaseTimeEntity을 상속할 경우 필드들(createdDate, modifiedDate)도 칼럼으로 인식하도록 합니다

@EntityListeners(AuditingEntityListener.class)

* BaseTimeEntity 클래스에 Auditing 기능을 포함시킴

@CreatedDate

* Entity가 생성되어 저장될 때 시간이 자동 저장됩니다.

@LastModifiedDate

* 조회한 Entity의 값을 변경할 때 시간이 자동 저장됨

그리고 Posts 클래스가 BaseTimeEntity를 상속받도록 설정

마지막으로 JPA Auditing을 활성화하기 위해서

/src/main/java/com/kyu/book/springboot/application.class

```java
@EnableJpaAuditing
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

작동하는지 확인하기 위해서 test코드작성

src/test/java/com/kyu/book/springboot/domain/posts/PostRepository.class

```java
@Test
public void BaseTimeEntity_enroll(){
    LocalDateTime now = LocalDateTime.of(2021,1,5,0,0,0);
    postsRepository.save(Posts.builder().title("title").content("content").author("author").build());

    List<Posts> postsList = postsRepository.findAll();

    Posts posts = postsList.get(0);

    System.out.println(">>>>>>> createDate="+posts.getCreatedDate()+", modifiedDate="+posts.getModifiedDate());

    assertThat(posts.getCreatedDate()).isAfter(now);
    assertThat(posts.getModifiedDate()).isAfter(now);
}
```
