# Day 4

Day4

## 등록/수정/조회 API 만들기

#### 👉API를 만들기 위해 총 3개의 클래스가 필요합니다

* Request 데이터를 받은 DTO
* API 요청을 받을 Controller
* 트랜잭션, 도메인 기능 간의 순서를 보장하는 Service

✅service는 트랜잭션, 도메인 간의 순서를 보장

Web Layer, Service Layer, Repository Layer, Dtos, Domain Model

* Web Layer
  * 흔히 사용하는 컨트롤러와 JSP/Freemarker 등의 뷰 템플릿 영역
  * 이외에도 @Filter, 인터셉터, 컨트롤러 어드바이스(@ControllerAdvice)등 외부 요청과 응답에 대한 전반적인 영역을 이야기합니다
* Service Layer
  * @Service에 사용되는 서비스 영역
  * 일반적으로 Controller와 Dao의 중간 영역에서 사용
  * @Transactional이 사용되어야 하는 영역
* Repository Layer
  * db와 같이 데이터 저장소에 접근하는 영역
  * 기존의 DAO라고 생각하면 됨
* Dtos
  * Dto(Data Transfer Object)는 계층 간에 데이터 교환을 위한 객체를 이야기하며
* Domain Model
  * 도메인이라고 불리는 개발 대상을 모든 사람이 동일한 관점에서 이해할 수 있고 공유할 수 있도록 단순화 시킨 것
  * @Entity가 사용된 영역은 도메인 모델
  * 무조건 db의 테이블과 관계가 있어야만 하는 것은 아님

✅web, service, repository, dto, domain이 5가지의 레이어에서 비지니스로직을 처리하는 곳은 **Domain**

✅서비스는 트랜잭션과 도메인 간의 순서만 보장

#### 👉코드 작성

/src/main/java/com/kyu/book/springboot/service/posts/PostsService.class

```java
package com.kyu.book.springboot.service.posts;

import com.kyu.book.springboot.domain.posts.PostsRepository;
import com.kyu.book.springboot.web.dto.PostsSaveRequestDto;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;

import javax.transaction.Transactional;

@RequiredArgsConstructor
@Service
public class PostsService {
    private final PostsRepository postsRepository;

    @Transactional
    public Long save(PostsSaveRequestDto requestDto){
        return postsRepository.save(requestDto.toEntity()).getId();
    }
}
```

스프링에서 Bean을 주입받는 방식

* @Autowired
* setter
* 생성자

✅ 가장 권장하는 방식 : 생성자 ⇒ @RequiredArgsConstructor로 해결

@RequiredArgsConstructor는 final이 선언된 모든 필드를 인자값으로 생성하는 생성자

Controller와 Service에서 사용할 Dto클래스

src/main/java/com/kyu/book/springboot/web/dto/PostsSaveRequestDto.java

```java
package com.kyu.book.springboot.web.dto;

import com.kyu.book.springboot.domain.posts.Posts;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

@Getter
@NoArgsConstructor
public class PostsSaveRequestDto {
    private String title;
    private String content;
    private String author;

    @Builder
    public PostsSaveRequestDto(String title, String content, String author){
        this.title = title;
        this.content = content;
        this.author = author;
    }

    public Posts toEntity(){
        return Posts.builder().title(title).content(content).author(author).build();
    }
}
```

Entity클래스는 db와 맞닿은 핵심 클래스, Entity클래스를 기준으로 테이블이 생성되고, 스키마가 변경됨

✅ View Layer와 DB Layer의 역할 분리를 철저하게 하는 것이 좋음 : Controller에서 결과값으로 여러 테이블을 조인해서 줘야할 경우가 빈번하기 떄문에

⇒Entity 클래스와 Controller에서 쓸 Dto는 분리해서 사용하라

Entity클래스를 테스트하는 테스트 코드

src/test/java/com/kyu/book/springboot/web/PostsApiControllerTest.java

```java
package com.kyu.book.springboot.web;

import com.kyu.book.springboot.domain.posts.Posts;
import com.kyu.book.springboot.domain.posts.PostsRepository;
import com.kyu.book.springboot.web.dto.PostsSaveRequestDto;
import org.junit.After;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.boot.web.server.LocalServerPort;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.test.context.junit4.SpringRunner;

import java.util.List;

import static org.assertj.core.api.AssertionsForClassTypes.assertThat;

@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class PostsApiControllerTest {
    @LocalServerPort
    private int port;

    @Autowired
    private TestRestTemplate restTemplate;

    @Autowired
    private PostsRepository postsRepository;

    @After
    public void tearDown() throws Exception{
        postsRepository.deleteAll();
    }

    @Test
    public void postEnroll() throws Exception{
        String title = "title";
        String content = "content";
        PostsSaveRequestDto requestDto = PostsSaveRequestDto.builder().title(title).content(content).author("author").build();

        String url = "http://localhost:" + port + "/api/v1/posts";

        ResponseEntity<Long> responseEntity = restTemplate.postForEntity(url, requestDto, Long.class);

        assertThat(responseEntity.getStatusCode()).isEqualTo(HttpStatus.OK);
        assertThat(responseEntity.getBody()).isGreaterThan(0L);

        List<Posts> all = postsRepository.findAll();
        assertThat(all.get(0).getTitle()).isEqualTo(title);
        assertThat(all.get(0).getContent()).isEqualTo(content);
    }
}
```

여기서는 @WebMvcTest를 사용하지 않은 이유 : 사용하게 되면 JPA기능이 작동하지 않음

JPA기능까지 한 번에 테스트하고 싶을 때는 @SpringBootTest와 @TestRestTemplate을 사용하라

### 수정 / 조회기능

src/main/java/com/kyu/book/springboot/web/PostsApiController.java

```java
package com.kyu.book.springboot.web;

import com.kyu.book.springboot.service.posts.PostsService;
import com.kyu.book.springboot.web.dto.PostsResponseDto;
import com.kyu.book.springboot.web.dto.PostsSaveRequestDto;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.*;

@RequiredArgsConstructor
@RestController
public class PostsApiController {
    private final PostsService postsService;

    @PostMapping("/api/v1/posts")
    public Long save(@RequestBody PostsSaveRequestDto requestDto){
        return postsService.save(requestDto);
    }

    @PutMapping("/api/v1/posts/{id}")
    public Long update(@PathVariable Long id, @RequestBody PostsUpdateRequestDto requestDto){
        return postsService.update(id, requestDto);
    }

    @GetMapping("/api/v1/posts/{id}")
    public PostsResponseDto findById(@PathVariable long id){
        return postsService.findById(id);
    }
}
```

src/main/java/com/kyu/book/springboot/web/dto/PostsResponseDto.class

```java
package com.kyu.book.springboot.web.dto;

import com.kyu.book.springboot.domain.posts.Posts;
import lombok.Getter;

@Getter
public class PostsResponseDto {
    private Long id;
    private String title;
    private String content;
    private String author;

    public PostsResponseDto(Posts entity){
        this.id = entity.getId();
        this.title = entity.getTitle();
        this.content = entity.getContent();
        this.author = entity.getAuthor();
    }
}
```

src/main/java/com/kyu/book/springboot/web/PostsUpdateRequestDto.class

```java
package com.kyu.book.springboot.web.dto;

import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

@Getter
@NoArgsConstructor
public class PostsUpdateRequestDto {
    private String title;
    private String content;

    @Builder
    public PostsUpdateRequestDto(String title, String content){
        this.title = title;
        this.content = content;
    }
}
```

src/main/java/com/kyu/book/springboot/domain/posts/Posts 에 이거 추가

```java
public void update(String title, String content){
    this.title = title;
    this.content = content;
}
```

src/main/java/com/kyu/book/springboot/service/posts/PostsService 에 이거 추가

```java
@Transactional
public Long update(Long id, PostsUpdateRequestDto requestDto){
    Posts posts = postsRepository.findById(id).orElseThrow(() -> new IllegalArgumentException("해당 게시글이 없습니다. id="+id));
    posts.update(requestDto.getTitle(), requestDto.getContent());

    return id;
}

public PostsResponseDto findById(Long id){
    Posts entity = postsRepository.findById(id).orElseThrow(() -> new IllegalArgumentException("해당 게시글이 없습니다. id="+id));

    return new PostsResponseDto(entity);
}
```

update 기능에서 db에 쿼리문을 안날림 ⇒ JPA의 영속성 컨텍스트 때문에 필요 없음

#### 영속성 컨텍스트 : 엔티티를 영구 저장하는 환경

ㄴJPA의 핵심 내용은 엔티티가 영속성 컨텍스트에 포함되어 있냐 아니냐로 갈림

JPA의 엔티티 매니저가 활성화된 상태(JPA사용시 기본 옵션) 시, 트랜잭션 안에서 데이터베이스에서 데이터를 가져오면 이 데이터는 영속성 컨텍스트가 유지된 상태이다

이 상태에서 해당 데이터의 값을 변경하면 트랜잭션이 끝날때 테이블에 변경문을 반영

즉, Entity 객체의 값만 변경하면 별도로 Update쿼리를 날릴 필요가 없다 = 더티 체킹

update를 추가했으니 테스트 코드를 작성

src/test/java/com/kyu/book/springboot/web/PostsApiControllerTest 에 추가

```java
@Test
public void postUpdates() throws Exception{
    Posts savedPosts = postsRepository.save(Posts.builder().title("title").content("content").author("author").build());

    Long updateId = savedPosts.getId();
    String expectedTitle = "title2";
    String expectedContent = "content2";

    PostsUpdateRequestDto requestDto = PostsUpdateRequestDto.builder().title(expectedTitle).content(expectedContent).build();

    String url = "http://localhost:" + port + "/api/v1/posts/" + updateId;

    HttpEntity<PostsUpdateRequestDto> requestEntity = new HttpEntity<>(requestDto);

    ResponseEntity<Long> responseEntity = restTemplate.exchange(url, HttpMethod.PUT, requestEntity, Long.class);

    assertThat(responseEntity.getStatusCode()).isEqualTo(HttpStatus.OK);
    assertThat(responseEntity.getBody()).isGreaterThan(0L);

    List<Posts> all = postsRepository.findAll();
    assertThat(all.get(0).getTitle()).isEqualTo(expectedTitle);
    assertThat(all.get(0).getContent()).isEqualTo(expectedContent);
    
}
```

local에서는 db를 h2를 사용하고 메모리에서 실행하기 때문에 직접 접근하려면 웹 콘솔을 사용해야함

1.  application-properties에 다음을 추가

    ```java
    spring.h2.console.enabled=true
    ```
2. main을 실행한 후, [http://localhost:8080/h2-console](http://localhost:8080/h2-console) 로 접속
3. JDBC URL 부분을 jdbc:h2:mem:testdb 로 변경후, connect버튼 클릭
4. insert into posts(author, content, title) values ('author', 'content', 'title'); 을 집어 넣고 run
5. 우리가 생성한 [http://localhost:8080/api/v1/posts/1](http://localhost:8080/api/v1/posts/1) 로 들어가면 insert해둔 posts들이 나오게 됨
