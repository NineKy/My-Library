# Day 6

### 템플릿 엔진

* 지정된 템플릿 양식과 데이터가 합쳐져서 HTML문서를 출력하는 sw를 야기한다
* 서버에서 구동된다
* 서버 템플릿 엔진을 이용한 화면 생성은 서버에서 Java 코드로 문자열을 만든 뒤, 이 문자열을 HTML으로 변환하여 브라우저로 전달
  * 서버 → 테플릿 → java코드 → HTML → 브라우저전달

### 머스테치

* 수 많은 언어를 지원하는 가장 심플한 템플릿 엔진
* 문법이 다른 템플릿 엔진보다 심플
* 로직 코드를 작성할 수 없음 ⇒ View/Server의 역할이 분명하게 나눠짐
* .js와 .java 이렇게 2가지가 있어서 하나의 문법으로 클라이언트와 서버 모두 사용가능

## 템플릿 엔진은 화면 역할에 충실해야 한다!!

build.gradle

```jsx
compile('org.springframework.boot:spring-boot-starter-mustache')
```

머스테치 스타터 의존성을 등록(스프링 부트에서 공식으로 지원하는 템플릿 엔진)

파일 위치 : src/main/resource/templates

src/main/resource/templates/index.mustache

```html
<!DOCTYPE HTML>
<html>
<head>
    <title>스프링 부트 웹서비스</title>
    <meta http-equiv="Content-Type" content="text/html"; charset="UTF-8" />
</head>
<body>
    <h1>스프링 부트로 시작하는 웹 서비스</h1>
</body>
</html>
```

index.mustache를 렌더링해주는 컨트롤러

src/main/java/web/dto/IndexController.class

```java
package com.kyu.book.springboot.web.dto;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class IndexController {
    @GetMapping("/")
    public String index(){
        return "index";
    }
}
```

* mustache 스타터 덕분에 컨트롤러에서 문자열을 반환할 때
  * 앞의 경로(src/main/resources/template)
  * 뒤의 파일 확장자(.mustache)
  * 이 2개는 자동으로 지정
* 리턴 값인 "index"는 src/main/resources/templates/index.mustache로 변환되어 View Resolver가 처리

테스트 코드

src/test/java/com/kyu/book/springboot/web/IndexControllerTest.class

```java
package com.kyu.book.springboot.web;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.test.context.junit4.SpringRunner;

import static org.assertj.core.api.AssertionsForClassTypes.assertThat;

@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class IndexControllerTest {
    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    public void mainPageLoad(){
        String body = this.restTemplate.getForObject("/", String.class);

        assertThat(body).contains("스프링 부트로 시작하는 웹 서비스");
    }
}
```

* 실제로 URL이 호출되었을 떄, 내용이 제대로 호출되었는지 테스트
  * "스프링 부트로 시작하는 웹 서비스"라는 문장이 존재하는지 확인

## 게시글 등록 화면 만들기

부트스트랩을 이용

* 외부 CDN을 사용 → 프로젝트에 다운로드 받을 필요x, 단지 코드 한 줄만 추가하면 ㅇㅋ
  * 하지만 실제 서비스에서는 사용하지 않는다 ⇒ CDN에 문제가 생기면 서비스에도 문제생김(안좋음)
* 직접 라이브러리를 받아서 사용

부트스트랩과 제이쿼리를 index.mustache에 추가 → 레이아웃 방식을 활용

* 레이아웃 방식 : 공통 영역을 별도의 파일로 불리하여 필요한 곳에서 가져다 사용하는 방식

src/main/resources/templates/layout에 footer.mustache, header.mustache를 추가

css는 header에, js는 footer에 ⇒ 페이지의 로딩속도를 높히기 위해

* head가 다 불러지지 않으면 사용자 쪽에선 백지 화면만 노출
* js의 용량이 크면 클수록 body부분의 실행이 늦어짐 ⇒ js는 body 하단에 두어 화면이 다 그려진 뒤에 호출
* css는 화면을 그리는 역할이기 떄문에 head에서 불러오는 것이 좋음
* 부트스트랩은 제이쿼리가 꼭 있어야하기 때문에 부트스트랩보다 먼저 호출하도록

footer와 header의 추가로 index에는 진짜 필요한 코드만 남게 됨

src/main/resources/templates/index.mustache

```html
{{>layout/header}}
    <h1>스프링 부트로 시작하는 웹 서비스</h1>
    <div class="col-md-12">
        <div class="col-md-6">
            <a href="/posts/save" role="button" class="btn btn-primary">글 등록</a>
        </div>
    </div>
{{>layout/footer}}
```

* {{>}}는 현재 mustache파일의 기준으로 다른 파일을 가져오는 것
