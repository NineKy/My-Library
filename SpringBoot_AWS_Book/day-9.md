# Day 9

## 스프링 시큐리티와 OAuth 2.0으로 로그인 구현

### 스프링 시큐리티

* 막강한 인증(Authentication)과 인가(Authorization) 기능을 가진 프레임워크
* 사실상 스프링 기반의 애플리케이션에서는 보안을 위한 표준

## 스프링 시큐리티와 스프링 시큐리티 Oauth2 클라이언트

현재 많은 서비스에서 로그인 기능을 소셜로그인 을 사용

freelec-springboot2-webservice라는 프로젝트를 생성,

OAuth 동의화면을 생성한 후,

사용자 인증 정보에 redirection url을 등록하고 새로 생성하게 되면

* 해당 프로젝트의 clientId와 security코드가 나오게 된다

이제는 현재까지 진행해오던 프로젝트에 application-oauth 설정파일을 만들어줘야 함

src/main/resources/application-oauth.properties

```java
spring.security.oauth2.client.registration.google.client-id = "클라이언트ID"
spring.security.oauth2.client.registration.google.client-secret = "비밀코드"
spring.security.oauth2.client.registration.google.scope = profile, email
```

* scope = profile, email
  * 많은 예제에서 실제로 scope을 등록하지 않고 사용한다
  * 기본값이 openid, profile, email이다
  * 하지만 openid라는 scope가 있으면 Open Id Provider로 인식하기 때문에 뺌
    * 이렇게 되면 OpenId Provider인 구글과 그렇지 않은 서비스(네이버/카카오)로 나눠서 OAuth2Service를 만들어야한다

스프링 부트에서는 properties의 이름을 application-관리할항목.properties로 만들게 되면

관리할항목 이름의 profile이 생성되어 이를 관리할 수 있게됨

profile=관리할항목 이렇게 호출하면 해당 properties의 설정들을 가져올 수 있음

[application.properties](http://application.properties) 에서 application-oauth.properties를 포함하도록 구성

```java
spring.profiles.include=oauth
```

* 이렇게 위에서 설정한 값들을 사용하는 것이 가능해짐

이제 민감한 정보들이 프로젝트에 들어가게 되면서 .gitignore에 추가해줘할 코드가 생김

```java
application-oauth.properties
```

추가해주고 commit 하면 [application-oauth.properties](http://application-oauth.properties) 이 파일은 stage에 안올라온 것을 확인가능

## 구글 로그인 연동하기

사용자 정보를 담당할 도메인인 User 클래스를 생성

src/main/java/com/kyu/book/springboot/domain/user

```java
package com.kyu.book.springboot.domain.user;

import com.kyu.book.springboot.domain.BaseTimeEntity;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

import javax.management.relation.Role;
import javax.persistence.*;

@Getter
@NoArgsConstructor
@Entity
public class User extends BaseTimeEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;

    @Column(nullable = false)
    private String email;

    @Column
    private String picture;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false)
    private Role role;

    @Builder
    public User(String name, String email, String picture, Role role){
        this.name = name;
        this.email = email;
        this.picture = picture;
        this.role = role;
    }
    
    public User update(String name, String picture){
        this.name = name;
        this.picture = picture;
        
        return this;
    }
    
    public String getRoleKey(){
        return this.role.getKey();
    }
}
```

* @Enumerated(EnumType.STRING)
  * JPA로 데이터베이스로 저장할 떄 Enum 값을 어떤 형태로 저장할지를 결정
  * 기본적으로는 int로 된 숫자가 저장
  * 숫자로 저장된 데이터베이스로 확인할 때 그 값이 무슨 코드를 의미하는지 알 수가 없음
  * 그래서 문자열(EnumType.STRING)로 저장될 수 있도록 선언합니다

각 사용자의 권한을 관리할 Enum 클래스 Role을 생성

src/main/java/com/kyu/book/springboot/domain/user/Role

```java
package com.kyu.book.springboot.domain.user;

import lombok.Getter;
import lombok.RequiredArgsConstructor;

@Getter
@RequiredArgsConstructor
public enum Role {
    GUESR("ROLE_GUEST", "손님"),
    USER("ROLE_USER", "일반 사용자");

    private final String key;
    private final String title;
}
```

* 스프링 시큐리티에는 권한 코드에 항상 ROLE\_ 이 앞에 있어야함
* 코드별 키 값을 ROLE\_GUEST, ROLE\_USER 등 으로 지정할 수 있음

User의 CRUD를 책임질 인터페이스 UserRepository도 생성

src/main/com/kyu/book/springboot/domain/user/UserRepository

```java
package com.kyu.book.springboot.domain.user;

import org.springframework.data.jpa.repository.JpaRepository;

import java.util.Optional;

public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);
}
```

* findByEmail
  * 소셜 로그인으로 반환되는 값 중 email을 통해 이미 생성된 사용자인지 처음 가입하는 사용인지 판단하기 위한 메소드

## 스프링 시큐리티 설정

build.gradle에 스프링 시큐리티 관련 의존성 하나를 추가

```java
compile('org.springframework.boot:spring-boot-starter-oauth2-client')
```

* spring-boot-starter-oauth2-client
  * 소셜 로그인 등 클라이언트의 입장에서 소셜 기능 구현 시 필요한 의존성
  * spring-security-oauth2-client와 spring-security-oauth2-jose를 기본으로 관리해준다

설정을 완료해 주었으니 OAuth 라이브러리를 이용한 소셜 로그인 설정 코드를 작성

config.auth패키지를 생성 ⇒ 시큐리티 관련 클래스를 담는 패키지

### 설정 코드 작성

src/main/java/kyu/book/springboot/config/auth/SecurityConfig

```java
package com.kyu.book.springboot.config.auth;

import com.kyu.book.springboot.domain.user.Role;
import lombok.RequiredArgsConstructor;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@RequiredArgsConstructor
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter{
    private final CustomOAuth2UserService customOAuth2UserService;

    protected void configure(HttpSecurity http) throws Exception{
        http
                .csrf().disable()
                .headers().frameOptions().disable()
                .and()
                    .authorizeRequests()
                    .antMatchers("/", "/css/**", "/images/**","/js/**", "/h2-console/**").permitAll()
                    .antMatchers("/api/v1/**").hasRole(Role.USER.name())
                    .anyRequest().authenticated()
                .and()
                    .logout()
                        .logoutSuccessUrl("/")
                .and()
                    .oauth2Login()
                        .userInfoEndpoint()
                            .userService(customOAuth2UserService);
    }
}
```

and()함수가 무엇인지?

* @EnableWebSecurity
  * Spring Security 설정들을 활성화 시켜줌
* csrf().disable().headers().frameOptions().disable()
  * h2-console 화면을 사용하기 위해 해당 옵션들을 disable
* authorizeRequests
  * URL별 권한 관리를 설정하는 옵션의 시작점
  * authorizeRequests가 선언 되어야만 antMatchers 옵션을 사용 가능
* antMatchers
  * 권한 관리 대상을 지정하는 옵션
  * URL, HTTP 메소드별로 관리가 가능
  * "/" 등 지정된 URL들은 permitAll() 옵션을 통해 전체 열람 권한을 줌
  * "/api/v1/\*\*" 주소를 가진 API는 USER권한을 가진 사람만 가능하도록 했음
* anyRequest
  * 설정된 값들 이외 나머지 URL들을 나타냄
  * 여기서는 authenticated()을 추가하여 나머지 URL들은 모두 인증된 사용자들에게만 허용
  * 인증된 사용자 즉, 로그인한 사용자들을 이야기함
* logout().logoutSuccessURL("/")
  * 로그아웃 기능에 대한 여러 설정의 진입점
  * 로그아웃 성공 시 "/"로 이동
* oauth2Login
  * OAuth2 로그인 기능에 대한 여러 설정의 진입점
* userInfoEndPoint
  * OAuth2 로그인 성공 이후 사용자 정보를 가져올 때의 설정들을 담당
* userService
  * 소셜 로그인 성공 시 후속 조치를 진행할 UserService 인터페이스의 구현체를 통해서
  * 리소스 서버(소셜 서비스)에서 사용자 정보를 가져온 상태에서 추가로 진행하고자 하는 기능을 명시 가능

### 구글 로그인 이후 가져온 사용자의 정보(email, name, picture 등)을 기반으로 가입, 및 정보 수정 세션 저장등의 기능을 지원

src/main/java/com/kyu/book/springboot/config/auth/CustomOAuth2UserService

```java
package com.kyu.book.springboot.config.auth;

import com.kyu.book.springboot.domain.user.User;
import com.kyu.book.springboot.domain.user.UserRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.oauth2.client.userinfo.DefaultOAuth2UserService;
import org.springframework.security.oauth2.client.userinfo.OAuth2UserRequest;
import org.springframework.security.oauth2.client.userinfo.OAuth2UserService;
import org.springframework.security.oauth2.core.OAuth2AuthenticationException;
import org.springframework.security.oauth2.core.user.DefaultOAuth2User;
import org.springframework.security.oauth2.core.user.OAuth2User;
import org.springframework.stereotype.Service;

import javax.servlet.http.HttpSession;
import java.util.Collections;

@RequiredArgsConstructor
@Service
public class CustomOAuth2UserService implements OAuth2UserService<OAuth2UserRequest, OAuth2User> {
    private final UserRepository userRepository;
    private final HttpSession httpSession;

    @Override
    public OAuth2User loadUser(OAuth2UserRequest userRequest) throws OAuth2AuthenticationException {
        OAuth2UserService<OAuth2UserRequest, OAuth2User> delegate = new DefaultOAuth2UserService();
        OAuth2User oAuth2User = delegate.loadUser(userRequest);

        String registrationId = userRequest.getClientRegistration().getRegistrationId();
        String userNameAttributeName = userRequest.getClientRegistration().getProviderDetails().getUserInfoEndpoint().getUserNameAttributeName();

        OAuthAttributes attributes = OAuthAttributes.of(registrationId, userNameAttributeName, OAuth2User.getAttributes());

        User user = saveOrUpdate(attributes);
        httpSession.setAttribute("user", new SessionUser(user));

        return new DefaultOAuth2User(Collections.singleton(new SimpleGrantedAuthority(user.getRoleKey())),attributes.getAttributes(), attributes.getNameAttributesKey());
    }

    private User saveOrUpdate(OAuthAttributes attribues){
        User user = userRepository.findByEmail(attribues.getEmail()).map(entity -> entity.update(attributes.getName(), attributes.getPicture())).orElse(attributes.toEntity());
        return userRepository.save(user);
    }
}
```

* registrationId
  * 현재 로그인 진행 중인 서비스를 구분하는 코드
  * 지금까지는 구글만을 사용하기 때문에 필요없지만 나중에 네이버 로그인까지 연동 시 구분을 위해서필요
* userNameAttributeName
  * OAuth2 로그인 진행 시 키가 되는 필드 값을 이야기 = Primary Key
  * 구글에서는 기본적으로 코드를 지원(구글코드 : sub)하지만 네이버, 카카오는 기본지원을 하지 않음
  * 네이버 로그인과 구글 로그인을 동시에 지원할 때 사용
* OAuthAttributes
  * OAuth2UserService를 통해서 가져온 OAuth2User의 attribute를 담을 클래스
  * 네이버 등 다른 소셜 로그인도 이 클래스를 사용
* SessionUser
  * 세션에 사용자 정보를 저장하기 위한 Dto 클래스
  * 왜 User 클래스를 사용하지 않고 새로 만들어서 쓰는지 이후에 설명
* 구글 사용자 정보가 업데이트 되었을 때를 대비해서 update기능도 같이 구현

### Dto의 역할을 하는 OAuthAttributes

src/main/java/com/kyu/book/springboot/config/auth/dto/OAuthAttributes

```java
package com.kyu.book.springboot.config.auth.dto;

import com.kyu.book.springboot.domain.user.Role;
import com.kyu.book.springboot.domain.user.User;
import lombok.Builder;
import lombok.Getter;

import java.util.Map;

@Getter
public class OAuthAttributes {
    private Map<String, Object> attributes;
    private String name;
    private String nameAttributeKey;
    private String email;
    private String picture;

    @Builder
    public OAuthAttributes(Map<String, Object> attributes, String nameAttributeKey, String name, String email, String picture){
        this.attributes = attributes;
        this.nameAttributeKey = nameAttributeKey;
        this.name = name;
        this.email = email;
        this.picture = picture;
    }

    public static OAuthAttributes of(String registrationId, String userNameAttributename, Map<String, Object> attributes){
        return OAuthAttributes.builder()
                .name((String)attributes.get("name"))
                .email((String)attributes.get("email"))
                .picture((String)attributes.get("picture"))
                .attributes(attributes)
                .nameAttributeKey(userNameAttributename)
                .build();
    }

    public User toEntity(){
        return User.builder().name(name).email(email).picture(picture).role(Role.GUESR).build();
    }
}
```

* of()
  * OAuth2User에서 반환하는 사용자 정보는 Map이기 떄문에 값 하나하나를 반환해야함
* toEntity()
  * User엔티티를 생성
  * OAuthAttributes에서 엔티티를 생성하는 시점은 처음 가입할 때
  * 가입할 때의 기본 권한을 GUEST로 주기 위해서 role 빌더 값에는 Role.GUEST를 사용
  * OAuthAttributes 클래스 생성이 끝났으면 같은 패키지에서 SessionUser클래스를 생성

### SessionUser클래스를 추가

src/main/java/com/kyu/book/springboot/config/auth/dto/SessionUser

```java
package com.kyu.book.springboot.config.auth.dto;

import com.kyu.book.springboot.domain.user.User;
import lombok.Getter;

import java.io.Serializable;

@Getter
public class SessionUser implements Serializable {
    private String name;
    private String email;
    private String picture;

    public SessionUser(User user){
        this.name = user.getName();
        this.email = user.getEmail();
        this.picture = user.getPicture();
    }
}
```

* 인증된 사용자의 정보만 필요하기 때문에 name, email, picture만 필드로 선언

### User 클래스를 사용하지 않는 이유

세션에 대해서 사용하려고 하니까 User 클래스에 직렬화를 구현하지 않았다는 의미의 에러가 발생

→ 이 점을 해결하기 위해 User 클래스에 직렬화 코드를 넣으면 어떻게 될까?

→좀 그렇다 = User 클래스가 엔티티이기 때문

* 엔티티 클래스에는 언제 다른 엔티티와 관계가 형성될지 모르기 떄문에 좋지 않다
  * 만약 자식 엔티티를 가지고 있다면 직렬화 대상에 자식들도 포함이 되기 떄문에 성능 이슈, 부수 효과가 발생

→ 그렇기 때문에 그냥 직렬화 기능을 가진 세션 dto를 하나 추가로 만드는 것이 더 좋음

## 로그인 테스트

src/main/resources/templates/index.mustache

```html
{{>layout/header}}
    <h1>스프링 부트로 시작하는 웹 서비스</h1>
    <div class="col-md-12">
        <div class="col-md-6">
            <a href="/posts/save" role="button" class="btn btn-primary">글 등록</a>
            {{#userName}}
                Logged in as: <span id="user">{{userName}}</span>
                <a href="/logout" class="btn btn-info active" role="button">Logout</a>
            {{/userName}}
            {{^userName}}
                <a href="/oauth2/authorization/google" class="btn btn-success active" role="button">Google</a>
            {{/userName}}
        </div>
        <br>
       ...
```

* {{#userName}}
  * 머스테치는 다른 언어와 같은 if문을 제공하지 않습니다
  * true/false 여부만 판단함
  * 머스테치에서는 항상 최종값을 넘겨줘야 한다
  * userName이 있다면 userName을 노출시키도록 구성
* a href="/logout"
  * 스프링 시큐리티에서 기본으로 제공하는 로그아웃 URL
    * 개발자가 별도로 logout에 해당하는 컨트롤러를 만들 필요가 없음
  * SecurityConfig를 통해서 url를 변경할 수는 있지만 기본 URL으로 충분하다
* {{^userName}}
  * 머스테치에서 해당 값이 존재하지 않는 경우에는 ^을 사용
  * 여기에서는 userName이 없다면 로그인 버튼을 노출시킨다는 의미
* a href="/oauth2/authorization/google"
  * 스프링 시큐리티에서 기본적으로 제공하는 로그인 URL
  * 로그아웃 URL과 마찬가지로 개발자가 별도의 컨트롤러를 만들 필요가 없음

이제는 index.mustache에서 userName을 사용할 수 있도록 하기 위해서는 IndexController에서 userName을 model에 저장하는 코드를 추가

src/main/java/com/kyu/book/springboot/web/dto/IndexController

```java
@GetMapping("/")
public String index(Model model){
    model.addAttribute("posts", postsService.findAllDesc());
    SessionUser user = (SessionUser)httpSession.getAttribute("user");
    if(user != null){
        model.addAttribute("username", user.getName());
    }
    return "index";
}
```

* (SessionUser)httpSession.getAttribute("user")
  * 앞 서 작성된 CustomOAuth2UserService에서 로그인 성공시 세션에 SessionUser를 저장하도록 구성
  * 로그인 성공 → httpSession.getAttribute("user")로 값을 가져옴
* if(user≠null)
  * 세션에 지정한 값이 있을 때만 model에 userName으로 등록
  * 세션에 저장된 값이 없다면 index.mustache에서 로그인 버튼이 보이지 않도록 해둠

이렇게까지 하고

애플리케이션을 실행시키고 → login을 해보자 → 구글로그인api로 넘어가고 → 로그인하게되면 → h2-console로 접근해서 user table에 회원가입되었고 → 정상적으로 이름을 가져와서 이름을 표시해준다

하지만 여기에서 글쓰는건 안되는데(json에서 에러가 403가 나오게 됨) → 이건 권한을 user로 맞춰두었기 떄문이다 → h2-console로 접근해서

```java
update user set role='USER';
```

로 권한을 USER로 변경해주고 → 다시 로그인하고 글쓰기해보면 정상적으로 가능!
