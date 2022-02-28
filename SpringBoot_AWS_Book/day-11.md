# Day 11

## 세션 저장소로 데이터베이스 사용하기

### 지금까지의 문제

*   현재까지의 서비스는 앱을 재실행하게 되면 로그인이 풀림 ⇒ 세션이 내장 톰캣의 메모리에 저장되기 때문

    \= 메모리에 저장되다 보니 내장 톰캣처럼 애플리케이션 실행 시 실행되는 구조에선 항상 초기화됨

    \= 배포할 때마다 톰캣이 재시작
*   만약 2대 이상의 서버에서 서비스하고 있다면 톰캣마다 세션의 동기화가 필요함

    \= 세션 저장소에 대해 방법 3가지

    * 톰캣 세션을 사용
      * 일반적으로 별 다른 설정이 없을때 기본적으로 선택되는 방식
      * 톰캣(WAS)에 세션이 저장되기 때문에 2대 이상의 WAS가 구동되는 환경에서는 톰캣들 간의 세션 공유를 위한 추가 설정이 필요
    * MySQL과 같은 데이터베이스를 세션 저장소로 사용
      * 여러 WAS 간의 공용 세션을 사용할 수 있는 가장 쉬운 방법
      * 많은 설정이 필요 없지만, 결국 로그인 요청마다 DB IO가 발생하여 성능상 이슈가 발생할 수 있음
      * 보통 로그인 요청이 많이 없는 백오피스, 사내 시스템 용도에서 사용
    * Redis, Memcached와 같은 메모리 DB를 세션 저장소로 사용
      * B2C 서비스에서 가장 많이 사용하는 방식
      * 실제 서비스로 사용하기 위해서는 Embedded Redis와 같은 방식이 아닌 외부 메모리 서버가 필요

### 2번째 방식인 데이터베이스를 세션 저장소로 사용하는 방식을 채택

👉 이유 : 설정이 간단, 사용자가 많은 서비스가 아니며 비용절감을 위해서

\+Redis와 같은 메모리 DB는 aws사용시 따로 사용료를 지불하면서 사용해야함

### spring-session-jdbc 등록

build.gradle에 의존성을 등록

build.gradle

```java
compile('org.springframework.session:spring-session-jdbc')
```

application-properties

```java
spring.session.store-type=jdbc
```

설정을 마쳤으니까 h2-console로 접속해서 세션을 위한 테이블(SPRING\_SESSION, SPRING\_SESSION\_ATTRIBUTES)가 생성된 것을 확인

→ JPA로 인해 세션 테이블이 자동으로 생성되었기 때문에 딱히 할일은 없음

기존과 동일하게 스프링을 재시작하면 세션이 풀린다 → h2도 재시작되기 때문임

* 하지만 aws에 업로드하게 되면 rds로 바뀌니까 세션이 안풀린다

### 네이버 로그인 추가

네이버 API로 이동해서 https://developers.naver.com/apps/#register?api=nvlogin

사용하고

[application-oauth.properties](http://application-oauth.properties)에 추가

[application-oauth.properties](http://application-oauth.properties)

```java
#registration
spring.security.oauth2.client.registration.naver.client-id = 클라이언트ID
spring.security.oauth2.client.registration.naver.client-secret = 클라이언트pw
spring.security.oauth2.client.registration.naver.redirect-uri={baseUrl}/{action}/oauth2/code/{registrationId}
spring.security.oauth2.client.registration.naver.authorization-grant-type=authorization_code
spring.security.oauth2.client.registration.naver.scope=name, email, profile_image
spring.security.oauth2.client.registration.naver.client-name = Naver

#provider
spring.security.oauth2.client.provider.naver.authorization-uri=https://nid.naver.com/oauth2.0/authorize
spring.security.oauth2.client.provider.naver.token-uri=https://nid.naver.com/oauth2.0/token
spring.security.oauth2.client.provider.naver.user-info-uri=https://openapi.naver.com/v1/nid/me
spring.security.oauth2.client.provider.naver.user_name_attribute=response
```

* user\_name\_attribute = response
  * 기준이 되는 user\_name의 이름을 네이버에서는 response로 해야한다
  * 이유는 네이버의 회원 조회 시 반환되는 JSON 형태

\-네이버의 응답값 최상위 필드는 resultCode, messge, response이다

그렇기 때문에 스프링 시큐리티에서 인식이 가능한 필드는 저 3개중에 하나여야한다

* 본문에서 담고 있는 response를 user\_name으로 지정
* 이후로 자바 코드로 response의 id를 user\_name으로 지정

### 스프링 시큐리티 설정 등록

OAuthAttributes에 네이버인지 판단하는 코드와 네이버 생성자를 추가

src/main/java/com/kyu/book/springboot/config/auth/dto/OAuthAttributes

```java
public static OAuthAttributes of(String registrationId, String userNameAttributeName, Map<String, Object> attributes){
        if("naver".equals(registrationId)){
            return ofNaver("id", attributes);
        }
        return ofGoogle(userNameAttributeName, attributes);
    }

    private static OAuthAttributes ofNaver(String userNameAttributeName, Map<String, Object> attributes){
        Map<String, Object> response = (Map<String, Object>) attributes.get("response");
        return OAuthAttributes.builder()
                .name((String)response.get("name"))
                .email((String)response.get("email"))
                .picture((String)response.get("profile_image"))
                .attributes(response)
                .nameAttributeKey(userNameAttributeName)
                .build();
    }
```

* 여기서

index.mustache에 네이버 로그인 버튼을 추가

src/main/java/resource/template/index.mustache

```html
...

{{^userName}}
    <a href="/oauth2/authorization/google" class="btn btn-success active" role="button">Google Login</a>
    <a href="/oauth2/authorization/naver" class="btn btn-success active" role="button">Naver Login</a>
{{/userName}}

...
```

* /oauth2/authorization/naver
  * 네이버 로그인 URL은 application-oauth.properties에 등록한 redirect-url 값에 맞춰서 자동으로 등록
  * /oauth2/authorization 까지는 고정이고 마지막 path만 각 소셜 로그인 코드를 사용
