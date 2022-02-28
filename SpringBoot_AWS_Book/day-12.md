# Day 12

## 기존 테스트에 시큐리티를 적용하기

기존에는 API를 바로 호출할 수 있었기 때문에 테스트 또한 API를 호출하도록 구성

만약 시큐리티 옵션이 활성화 하게 되면 인증된 사용자만 API를 호출할 수 있음

### 👉 그렇기 때문에 기존의 API테스트 코드마다 인증한 사용자가 호출한 것 처럼 작동하도록 수정

오른쪽의 Gradle창에서 Tasks/test를 실행해보면 모든 스프링을 이용한 테스트가 실패함

### 문제 1 : CustomOAuth2UserService를 찾을 수 없음

첫 번째 실패 테스트의 메세지를 보면 No qualifying bean of type 'com/kyu ... ' 임

👉 이는 CustomOAuth2UserServie를 생성하는데 필요한 소셜 로그인 관련 설정 값이 없기 때문

test에 appliation.properties가 없으면 main의 설정을 그대로 가져오기 때문에 여전히 안됨

(이렇게 자동으로 가져오는 옵션의 범위는 [application.properties](http://application.properties) 파일까지임)

실제로 구글 연동까지 할꺼는 아니기 떄문에 가짜 설정값을 등록

src/test/resources/application.properties 파일을 생성

```java
spring.jpa.show_sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.
dialect.MySQL5InnoDBDialect
spring.h2.console.enabled=true
spring.session.store-type=jdbc

#Test OAuth

spring.security.oauth2.client.registration.google.client-id = test
spring.security.oauth2.client.registration.google.client-secret = test
spring.security.oauth2.client.registration.google.scope = profile, email
```

### 문제 2 : 302 Status Code

응답의 결과로 200을 원했는데 결과는 302(리다이렉션 응답)이 와서 실패함

👉 이는 스프링 시큐리티 설정 때문에 인증되지 않은 사용자의 요청은 이동시키기 때문

그래서 이런 API 요청은 임의로 인증된 사용자를 추가해서 API만 테스트할 수 있도록 수정

스프링 시큐리티 테스트를 위한 여러가지 도구를 지원하는 : spring-security-test를 build.gradle에 추가

```java
testCompile('org.springframework.security:spring-security-test')
```

PostApiControllerTest에 2개의 테스트 메소드에 임이의 사용자 인증을 추가

posts\_enroll()과 post\_update()함수에

@WithMockUser(roles="USER") annotation을 추가

* 인증된 모의 사용자를 만들어서 사용
* roles에 권한을 추가할 수 있음
* 이 annotation으로 인해 ROLE\_USER권한을 가진 사용자가 API를 요청하는 것과 동일한 효과를 가지게 됨

실제로는 작동하지 않는다 ⇒ @WithMockUser가 MockMVC에서만 작동되기 때문

현재 PostApiControllerTest는 @SpringBootTest로만 되어있고 MockMVC를 전혀 사용하지 않기 때문에 @SpringBootTest에서 MockMvc를 사용하는 방법으로 수정해야함

```java
...

@Autowired
    private WebApplicationContext context;

    private MockMvc mvc;

...

@Before
    public void setup(){
        mvc = MockMvcBuilders
                .webAppContextSetup(context)
                .apply(springSecurity())
                .build();
    }

...

@Test
@WithMockUser(roles="USER")
public void postEnroll() throws Exception{
    String title = "title";
    String content = "content";
    PostsSaveRequestDto requestDto = PostsSaveRequestDto.builder().title(title).content(content).author("author").build();

    String url = "http://localhost:" + port + "/api/v1/posts";

    ResponseEntity<Long> responseEntity = restTemplate.postForEntity(url, requestDto, Long.class);

    assertThat(responseEntity.getStatusCode()).isEqualTo(HttpStatus.OK);
    assertThat(responseEntity.getBody()).isGreaterThan(0L);

    //when
    mvc.perform(post(url)
            .contentType(MediaType.APPLICATION_JSON_UTF8)
            .content(new ObjectMapper().writeValueAsString(requestDto))
    ).andExpect(status().isOk());
    
    //then
    List<Posts> all = postsRepository.findAll();
    assertThat(all.get(0).getTitle()).isEqualTo(title);
    assertThat(all.get(0).getContent()).isEqualTo(content);
}

@Test
    @WithMockUser(roles="USER")
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

        //when
        mvc.perform(post(url)
                .contentType(MediaType.APPLICATION_JSON_UTF8)
                .content(new ObjectMapper().writeValueAsString(requestDto))
        ).andExpect(status().isOk());

        //then
        List<Posts> all = postsRepository.findAll();
        assertThat(all.get(0).getTitle()).isEqualTo(expectedTitle);
        assertThat(all.get(0).getContent()).isEqualTo(expectedContent);

    }
```

* @Before
  * 매번 테스트가 시작되기 전에 MockMvc 인스턴스를 생성
* postEnroll과 postUpdate함수에 [//when부분을](https://xn--when-er1rya685o) 새로 추가해줌(mvc.perform)
  * 생성된 MockMvc를 통해 API를 테스트
  * 본문(Body) 영역은 문자열로 표현하기 위해 ObjectMapper를 통해서 문자열 JSON으로 변환

### 문제 3 : @WebMvcTest에서 CustomOAuth2UserService를 찾을 수 없음

첫 번째로 해결한 것과 같은 에러 메세지이지만 다른점이 있음

👉 @WebMvcTest를 사용했다는 점

* @WebMvcTest는 CustomOAuth2UserService를 스캔하지 않았기 때문
* @WebMvcTest는 WebSecurityConfigurerAdapter, WebMvcConfigurer를 비롯한 @ControllerAdvice, @Controller를 읽음
* 즉, @Repository, @Service, @Component는 스캔 대상이 아님

👉 문제를 해결하기 위해서 스캔 대상에서 SecurityConfig를 제거

src/test/java/com/kyu/book/springboot/web/dto/HelloControllerTest

```java
@WebMvcTest(controllers = HelloController.class,
        excludeFilters ={
        @ComponentScan.Filter(type = FilterType.ASSIGNABLE_TYPE, classes = SecurityConfig.class)
        })
```

\+여기서도 마찬가지로 @WithMockUser를 사용해서 가짜로 인증된 사용자를 생성

@Test가 붙어있는 함수에 @WithMockUser(roles="USER")를 추가

이렇게 까지하면 IllegalArgumentException이 발생!!!

* 이것은 @EnableJpaAuditin로 인해서 발생 → 최소 하나의 @Entity 클래스가 필요

@WebMvcTest이기 때문에 당연히 없음

@EnableJpaAudting가 @SpringBootApplication와 함께 있다보니까 @WebMvcTest에서도 스캔하게 됨

→ @EnableJapAuditing과 @SpringBootApplication 둘을 분리

src/main/java/com/kyu/book/springboot/Application 에서 @EnableJpaAuditing을 제거

그리고 config 패키지에 JpaConfig을 생성해서 @EnableJpaAuditing을 추가

이렇게 까지하고 Test를 돌려보면 정상적으로 모든 테스트가 실행되는 것을 확인할 수 있다
