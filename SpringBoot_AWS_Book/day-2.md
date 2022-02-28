# Day 2

저번 시간에 이어서 테스트 코드를 실행해보기 위한 과정

## 👉저번에 만들었던 테스트를 위한 코드를 검증

테스트 코드를 만들기 위해서는

* src/test/java 디렉토리로 가서
* 테스트하려는 코드가 들어있는 패키지와 똑같은 패키지를 생성하고
* 테스트하려는 클래스의 이름에 Test를 붙힌 이름으로 테스트코드를 작성한다

src/test/java/com/kyu/book/springboot/HelloControllerTest.class

```java
package com.kyu.book.springboot;

import com.kyu.book.springboot.web.HelloController;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@RunWith(SpringRunner.class)
@WebMvcTest(controllers = HelloController.class)
public class HelloControllerTest {
    @Autowired
    private MockMvc mvc;

    @Test
    public void REHello() throws Exception{
        String hello = "hello";

        mvc.perform(get("/hello"))
                .andExpect(status().isOk())
                .andExpect(content().string(hello));
    }
}
```

@RunWith(SpringRunner.class)

* 테스트를 진행할 때 JUnit에 내장된 실행자 외에 다른 실행자를 실행
* 여기서는 SpringRunner라는 스프링 실행자를 사용
* 스프링 부트 테스트와 JUnit 사이의 연결자 역할을 수행

@WebMvcTest

* 선언하게 되면 → @Controller, @ControllerAdvice 등의 annotation을 사용할 수 있음
  * @Service, @Repository, @Component등의 annotation은 사용할 수 없음
* 여러 스프링 annotation중에서 Web에 집중할 수 있는 annotation이다
* 여기에서는 컨트롤러만 사용하기 때문에 선언함

@Autowired

* 스프링이 관리하는 Bean을 주입받는다

Mockmvc

* 웹 API를 테스트할 때 사용함
* 스프링 MVC 테스트의 시작점
* 이 클래스를 통해 HTTP GET, POST 등에 대한 API 테스트를 할 수 있음
* perform(get("/hello"))
  * MockMvc를 통해 /hello 주소로 http get요청
  * 메소드 체이닝이 가능해 여러 검증을 이어서 가능
    * andExpect(status().isOk())
      * perform의 결과값을 검증
      * http header의 status를 검증한다 → 200, 404, 500와 같이 상태 검증
      * isOk()는 상태 코드가 200인지 아닌지를 검증
    * andExpect(content().string(hello))
      * mvc.perform의 결과를 검증
      * 응답 본문의 내용을 검증한다
      * Controller(테스트하는 코드)에서 hello를 리턴하기 때문에 이 값이 맞는지 확인

## 👉Lombok

gradle에 추가

* compile('org:projectlombock:lombok')

plugins에 추가

롬복은 프로젝트마나 설정을 해줘야함 ⇒ build.gradle에 추가 / Settings > Build > Compiler > Annotation processors > Enable annotation processing

***

위의 방식으로 gradle에 추가하려니까 안됨

그래서

build.gradle의 compile하는 dependencies에 아래와 같이 넣어주니까 잘 됨

```java
annotationProcessor("org.projectlombok:lombok")
compileOnly("org.projectlombok:lombok")
```

## 👉HelloController코드를 롬복으로 변환해보기

web아래에 dto패키지 추가

* 모든 응답 dto는 이 dto패키지에 추가할 예정

src/main/java/com/kyu/book/springboot/web/dto/HelloResponseDto.class

```java
package com.kyu.book.springboot.web.dto;

import lombok.Getter;
import lombok.RequiredArgsConstructor;

@Getter
@RequiredArgsConstructor
public class HelloResponseDto {
    private final String name;
    private final int amount;
}
```

@RequestArgsConstructor

* 선언된 모든 final 필드가 포함된 생성자를 생성해줌
* final이 없는 필드는 생성자에 포함되지 않음

위의 코드의 테스트코드

src/main/java/com/kyu/book/springboot/web/dto/HelloResponseDtoTest.class

```java
package com.kyu.book.springboot.web.dto;

import org.junit.Test;
import static org.assertj.core.api.Assertions.assertThat;

public class HelloResponseDtoTest {
    @Test
    public void lm_fn_test(){
        String name = "test";
        int amount = 1000;

        HelloResponseDto dto = new HelloResponseDto(name, amount);

        assertThat(dto.getName()).isEqualTo(name);
        assertThat(dto.getAmount()).isEqualTo(amount);
    }
}
```

\*이 코드를 진행하는 과정에서 lombok의 @Getter가 잘 안먹였었다 ⇒ 그래서 plugin에 들어가서 lombok을 disabled > apply 이후, enabled > apply 하니까 잘됨

\*test 진행이 불가능했다 ⇒ github에 질문페이지를 가서 찾아보니까 gradle의 버전문제가 있었다.

여전히 현업에서도 gradle4를 사용한다고 하니까 그려러니하고 gradle4를 사용하자

버전이 이미 올라가있을때 변경하는방법은

terminal을 열어서

```java
./gradlew wrapper —gradle-version 4.10.2
```

를 적용해주면 정상적으로 테스트가 진행된다

assertThat

* assertj라는 테스트 검증 라이브러리의 검증 메소드
* 검증하고 싶은 대상의 메소드 인자를 받는다
* 메소드 체이닝 지원됨
* isEqualTo()
  * 동등한지 비교하는 메소드
  * 값이 같으면 성공
* assertj의 장점 : 백기선님 유튜브를 보고 배우자
  * [https://www.youtube.com/watch?v=zLx_fI24UXM](https://www.youtube.com/watch?v=zLx_fI24UXM)

## 👉ResponseDto를 사용하도록 추가

src/main/java/com/kyu/boot/springboot/web/dto/HelloResponseDto.class에 추가된코드

```java
@GetMapping("/hello/dto")
public HelloResponseDto helloDto(@RequestParam("name")String name, 
																		@RequestParam("amount")int amount){
    return new HelloResponseDto(name, amount);
}
```

@RequestParam

* 외부에서 API로 넘긴 파라미터를 가져오는 annotation입니다.
* 여기에서는 외부에서 name(@RequestParam("name")) 이란 이름으로 넘긴 파라미터를 메소드 파라미터 name(String name)에 저장하게 됩니다.

## 👉추가된 API를 테스트

src/test/java/com/kyu/book/springboot/web/HelloControllerTest.class에 추가된 코드

```java
@Test
public void REHelloDto() throws Exception{
    String name = "hello";
    int amount = 1000;

    mvc.perform(
            get("/hello/dto")
                .param("name", name)
                .param("amount", String.valueOf(amount))
    )
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.name", is(name)))
            .andExpect(jsonPath("$.amount", is(amount)));
}
```

param

* API 테스트할 때 사용될 요청 파라미터를 설정
* 단, 값은 String만 허용
* 그래서 숫자/날짜 등의 데이터도 등록할 때는 문자열로 변경해야만 가능

jsonPath

* JSON 응답값을 필드별로 검증할 수 있는 메소드
* $를 기준으로 필드명을 명시
* 여기선 name, amount를 검증하니 $.name, $.amount로 검증

\*자꾸 테스트시 Test events were not received가 뜸

⇒ Preference > Build, Execution, Deployment > Build Tools > Gradle로 이동해서

⇒Run test using을 Intellij IDEA로 변경
