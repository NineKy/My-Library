# Day 7

### 이전에 만들었던 mustache 템플릿의 컨트롤러를 만들어보자

페이지에 관련된 컨트롤러는 IndexController에 추가

src/main/java/com/kyu/book/springboot/web/dto/IndexController

```java
@GetMapping("/posts/save")
public String postSave(){
    return "posts-save";
}
```

* 여기서 posts-save를 호출하는 코드를 추가

글을 등록하는 페이지를 생성

src/main/resource/template/posts-save.mustache

```java
{{>layout/header}}

<h1>게시글 등록</h1>

<div class="col-md-12">
    <div class="col-md-4">
        <form>
            <div class="form-group">
                <label for="title">제목</label>
                <input type="text" class="form-control" id="title" placeholder="제목을 입력하세요">
            </div>
            <div class="form-group">
                <label for="author"> 작성자 </label>
                <input type="text" class="form-control" id="author" placeholder="작성자를 입력하세요">
            </div>
            <div class="form-group">
                <label for="content"> 내용 </label>
                <textarea class="form-control" id="content" placeholder="내용을 입력하세요"></textarea>
            </div>
        </form>
        <a href="/" role="button" class="btn btn-secondary">취소</a>
        <button type="button" class="btn btn-primary" id="btn-save">등록</button>
    </div>
</div>

{{>layout/footer}}
```

현재까지는 localhost:8080으로 접속 → 글 등록(버튼)을 누름 → posts-save.mustache로 이동되서 글 등록하는 페이지를 볼 수 있음

글을 등록하는 버튼을 누르면 API를 호출해주는 js파일을 생성해야함

src/main/resources/static/js/app/index.js

```jsx
var main = {
    init : function(){
        var _this = this;
        $('#btn-save').on('click', function(){
            _this.save();
        });
    },
    save : function(){
        var data = {
            title: $('#title').val(),
            author: $('#author').val(),
            content: $('#content').val()
        };

        $.ajax({
            type: 'POST',
            url: '/api/v1/posts',
            dataType: 'json',
            contentType: 'application/json; charset=utf-8',
            data: JSON.stringify(data)
        }).done(function(){
            alert('글이 등록되었습니다.');
            window.location.href='/';
        }).fail(function(error){
            alert(JSON.stringify(error));
        });
    }
};
main.init();
```

원래 만들어두었던 API를 호출하는 javascript파일

브라우저의 스코프는 공용 공간으로 쓰이기 떄문에 나중에 로딩된 js의 init, save가 먼저 로딩된 js의 function을 덮어쓰게 된다 ⇒ 그래서 index.js만의 유효범위(scope)를 만들어서 사용

var index라는 객체를 만들어서 해당 객체에서 필요한 모든 function을 선언했음 ⇒ index객체 안에서만 function이 유효하기 떄문에 다른 js와는 겹칠 위험이 사라지게 됨

현재 index의 호출 코드를 보면 /(절대경로)에서 시작

* 스프링부트는 기본적으로 src/main/resource/static에 위치한 js, css 등 정적인 파일들을 URL에서 /로 설정

**등록 버튼을 누르면 안눌리는 문제가 있다**

*   resources에서 static/js/app 디렉토리를 만들고 안에 index.js를 생성했는데 IDE에서 볼때 구분이 . 으로 되어있어서 아무생각없이 . 으로 디렉토리를 생성했었다

    ⇒결과적으로 static/js/app 이름으로 디렉토리를 다시 생성해줌

### 전체 조회 화면 만들기

index.mustachedml UI를 변경

src/main/resources/templates/index.mustache

```html
{{>layout/header}}
    <h1>스프링 부트로 시작하는 웹 서비스</h1>
    <div class="col-md-12">
        <div class="col-md-6">
            <a href="posts/save" role="button" class="btn btn-primary">글 등록</a>
        </div>
        <br>
        <table class="table table-horizontal table-bordered">
            <thread class="thead-strong">
                <tr>
                    <th>게시글번호</th>
                    <th>제목</th>
                    <th>작성자</th>
                    <th>최종수정일</th>
                </tr>
            </thread>
            <tbody id="tbody">
            {{#posts}}
                <tr>
                    <td>{{id}}</td>
                    <td>{{title}}</td>
                    <td>{{author}}</td>
                    <td>{{modifiedDate}}</td>
                </tr>
            {{/posts}}
            </tbody>
        </table>
    </div>

{{>layout/footer}}
```

* 머스테치의 문법이 사용됨
  * {{#posts}} : posts라는 List를 순회한다
    * Java의 for문이라고 생각
  * {{id}}등의 변수명 : List에서 뽑아낸 객체의 필드를 사용

### Controller, Service, Repository 코드를 작성

기존에 있던 Repository인터페이스에 쿼리가 추가됨

src/main/java/com/kyu/book/springboot/domain/posts/PostsRepository

```java
package com.kyu.book.springboot.domain.posts;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;

import java.util.List;

public interface PostsRepository extends JpaRepository<Posts, Long> {
    @Query("SELECT p FROM Posts p ORDER BY p.id DESC ")
    List<Posts> findAllDesc();
}
```

* 처음으로 쿼리문을 작성하게 되었고 쿼리문을 사용하기 위해서 @Query 태그를 사용
* 데이터의 조회가 복잡해지면
  * read는 MyBatis 를 사용
  * create, update, delete는 SpringDataJPA를 사용

다음으로는 PostsService에 코드를 추가

src/main/java/com/kyu/book/springboot/service/posts/PostsService

```java
@Transactional(readOnly = true)
public List<PostsListResponseDto> findAllDesc(){
    return postsRepository.findAllDesc().stream().map(PostsListResponseDto::new).collect(Collectors.toList());
}
```

* @Transactional(readOnly = true)이렇게 옵션을 추가
  * readOnly = true 는 트랜젝션의 범위는 유지하되, 조회 기능만 남겨두는 것
  * 조회 속도가 개선
  * create, update, delete기능이 전혀 없는 서비스 메소드에서 사용하는 것을 추천
* 여기서 PostsListResponseDto가 없음

바로 여기서 생성해줌

src/main/java/com/kyu/book/springboot/web/dto/PostsListResponseDto

```java
package com.kyu.book.springboot.web.dto;

import com.kyu.book.springboot.domain.posts.Posts;
import lombok.Getter;
import com.fasterxml.jackson.annotation.JsonFormat;
import java.time.LocalDateTime;

@Getter
public class PostsListResponseDto {
    private Long id;
    private String title;
    private String author;
    private LocalDateTime modifiedDate;

    public PostsListResponseDto(Posts entity){
        this.id = entity.getId();
        this.title = entity.getTitle();
        this.author = entity.getAuthor();
        this.modifiedDate = entity.getModifiedDate();
    }
}
```

읽었으니까 읽은 것을 출력해주기 위해서 IndexController 수정

src/java/main/kyu/book/springboot/web/dto/IndexController

```java
package com.kyu.book.springboot.web.dto;

import com.kyu.book.springboot.service.posts.PostsService;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@RequiredArgsConstructor
@Controller
public class IndexController {
    private final PostsService postsService;

		//변경사항이 있는 곳
    @GetMapping("/")
    public String index(Model model){
        model.addAttribute("posts", postsService.findAllDesc());
        return "index";
    }
		//////////////////
    @GetMapping("/posts/save")
    public String postSave(){
        return "posts-save";
    }
}
```

* Model
  * 서버 템플릿 엔진에서 사용할 수 있는 객체를 저장할 수 있음
  * 여기에서는 postsService.findAllDesc()로 가져온 결과를 posts로 index.mustache에 전달한다
