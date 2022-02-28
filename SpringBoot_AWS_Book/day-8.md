# Day 8

## 게시글 수정, 삭제 화면 만들기

수정 API는 이미 만들어두었음

### 게시글 수정 화면 mustache 파일을 생성

src/main/resources/templates/posts-update.mustache

```html
{{>layout/header}}

<h1>게시글 수정</h1>

<div class="col-md-12">
    <div class="col-md-4">
        <form>
            <div class="form-group">
                <label for="title">글 번호</label>
                <input type="text" class="form-control" id="id" value="{{post.id}}" readonly>
            </div>
            <div class="form-group">
                <label for="title">제목</label>
                <input type="text" class="form-control" id="title" value="{{post.title}}">
            </div>
            <div class="form-group">
                <label for="author"> 작성자 </label>
                <input type="text" class="form-control" id="author" value="{{post.author}}" readonly>
            </div>
            <div class="form-group">
                <label for="content"> 내용 </label>
                <textarea class="form-control" id="content">{{post.content}}</textarea>
            </div>
        </form>
        <a href="/" role="button" class="btn btn-secondary">취소</a>
        <button type="button" class="btn btn-primary" id="btn-update">수정 완료</button>
        <button type="button" class="btn btn-danger" id="btn-delete">삭제</button>
    </div>
</div>

{{>layout/footer}}
```

* {{post.id}}
  * mustache는 객체의 필드의 접근 시 . 으로 구분한다
  * Post클래스의 id에 대한 접근은 [post.id](http://post.id) 이렇게 작성한다
* readonly
  * input 태그에 읽기 가능만 허용하는 속성
  * id와 author는 수정할 수 없도록 읽기만 허용하도록 추가

btn-update 버튼을 누르면 update 기능을 호출할 수 있도록 index.js 파일에도 update function을 추가

src/resources/static/js/app/index.js

```jsx
init : function () {
        var _this = this;
        $('#btn-save').on('click', function () {
            _this.save();
        });
        $('#btn-update').on('click', function(){
            this.update();
        });
    },

...

update : function(){
    var data = {
        title: $('#title').val(),
        content: $('#content').val()
    };

    var id = $('#id').val();
    $.ajax({
        type: 'PUT',
        url: '/api/v1/posts/'+id,
        dataType: 'json',
        contentType: 'application/json; charset=utf-8',
        data: JSON.stringify(data)
    }).done(function(){
        alert('글이 수정되었습니다');
        window.location.href='/';
    }).fail(function(error){
        alert(JSON.stringify(error))
    });
}
```

* $('#btn-update').on('click')
  * btn-update라는 id를 가진 HTML element의 click 이벤트가 발생할 떄 update function을 실행하라고 등록해둠
  * 이벤트 등록
* type: 'PUT'
  * HTTP Method중의 PUT메소드
  * PostsApiController에 있는 API에서 이미 @PutMapping으로 선언했기 떄문에 PUT을 사용해야함
  * REST에서는 CRUD를 HTTP Method와 매핑했음
    * Create - POST
    * Read - GET
    * Update - PUT
    * Delete - DELETE
* url:'/api/v1/posts/'+id
  * 어느 게시글을 수정할지 URL Path으로 구분하기 위해 Path에 id를 추가

마지막으로 전체 목록에서 수정 페이지로 이동할 수 있게 페이지 이동 기능을 추가

src/main/resources/templates/index.mustache

```html
{{#posts}}
    <tr>
        <td>{{id}}</td>
        <td><a href="/posts/update/{{id}}">{{title}}</a></td>
        <td>{{author}}</td>
        <td>{{modifiedDate}}</td>
    </tr>
{{/posts}}
```

* \- 타이틀을 클릭하면 해당 게시글의 수정화면으로 이동

