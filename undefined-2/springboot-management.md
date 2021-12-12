# SpringBoot Management

## Actuator

스프링 부트 공식문서([https://docs.spring.io/spring-boot/docs/2.1.8.RELEASE/reference/html/production-ready.html](https://docs.spring.io/spring-boot/docs/2.1.8.RELEASE/reference/html/production-ready.html))

> Spring Boot includes a number of additional features to help you monitor and manage your application when you push it to production. You can choose to manage and monitor your application by using HTTP endpoints or with JMX. Auditing, health, and metrics gathering can also be automatically applied to your application.

스프링 부트에는 애플리케이션을 운영에 올렸을 때, 해당 애플리케이션을 모니터링 해주고 관리해주는 다수의 기능들이 있다 그것을 Actuator라고 부른다. 사용자는 모니터링, 관리해주는 기능인 actuator를 HTTP endpoints나 JMX을 통해서 사용할 수 있다.

### 적용

```java
compile("org.springframework.boot:spring-boot-starter-actuator")
compile("org.springframework.boot:spring-boot-starter-web")
```

요렇게 2개의 의존성을 추가해주고 애플리케이션을 실행하고

/actuator

요 주소로 접속하게 되면 현재 애플리케이션을 통해서 접속할 수 있는 모든 엔드포인트들에 대한 정보들을 제공해준다 → parse되지 않은 상태로 출력되기 때문에 브라우저에서 json parser 플러그인을 통해서 더 가독성 좋게 확인하자

기본적으로 제공해주는 엔드 포인트들은 actuator, health, info가 있고 추가적으로 사용하는 모든 엔드 포인트들은 actuator/{엔드포인트} 이렇게 사용한다.

actuator : 현재 애플리케이션에서 제공해주고 있는 모든 endpoint를 보여줌

health : 현재 구동 중인 애플리케이션의 건강 상태에 대해서 표시

info : 개발자가 정의한 애플리케이션의 정보들을 표시

### 사용 방법

[application.properties](http://application.properties)에 사용하고 싶은 걸 include, exclude하면 됨

```java
management.endpoints.web.exposure.include=health, info, beans, conidtions
management.endpoints.web.exposure.exclude=
```

상태는 크게 UP과 DOWN을 볼 수 있는데 UP은 시스템이 잘 작동 중이고 접근이 가능하다는 뜻이고 DOWN은 시스템이 작동 중이지 않고 접근할 수 없다는 의미이다

여러가지 actuator에서 제공해주는 다양한 엔드 포인트들이 존재하고 필요한 것들을 include에 추가해줘서 사용하면 되겠다..!

## Spring Boot Admin

요놈은 spring boot 에서 제공해주는 actuator를 깔끔한 UI로 모니터링 할 수 있도록 제공해주는 서비스이다

크게 서버와 클라이언트로 나눠지는데 → 항상 서버와 클라이언트의 버전을 맞춰줘야하는 것이 포인트\`

일단 서버 먼저

일단은 사용하기 위해서는 build.gradle에 추가 + spring security도 사용해야함

```java
implementation 'de.codecentric:spring-boot-admin-starter-server:2.4.1'
implementation 'org.springframework.boot:spring-boot-starter-security'
```

추가적으로 application.properties에 필요한 세팅들을 추가

```java
server.port=8090
spring.security.user.name=client
spring.security.user.password=client
```

이후에는 해당 애플리케이션의 @SpringBootApplication이 있는 위치에 @EnableAdminServer를 통해서 UI를 사용하겠다고 스프링에게 알려줌

```java
@EnableAdminServer
@SpringBootApplication
public class JpaProjectApplication {

    public static void main(String[] args) {
        SpringApplication.run(JpaProjectApplication.class, args);
    }

}
```

그리고 config 파일을 만들어서 시큐리티를 적용

```java
package config;

import de.codecentric.boot.admin.server.config.AdminServerProperties;
import lombok.RequiredArgsConstructor;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.HttpMethod;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.web.authentication.SavedRequestAwareAuthenticationSuccessHandler;
import org.springframework.security.web.csrf.CookieCsrfTokenRepository;
import org.springframework.security.web.util.matcher.AntPathRequestMatcher;

import java.util.UUID;

@Configuration
@RequiredArgsConstructor
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    private final AdminServerProperties adminServer;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        SavedRequestAwareAuthenticationSuccessHandler successHandler =
                new SavedRequestAwareAuthenticationSuccessHandler();
        successHandler.setTargetUrlParameter("redirectTo");
        successHandler.setDefaultTargetUrl(this.adminServer.getContextPath() + "/");

        http
                .authorizeRequests()
                .antMatchers(this.adminServer.getContextPath() + "/assets/**").permitAll()
                .antMatchers(this.adminServer.getContextPath() + "/login").permitAll()
                .anyRequest().authenticated()
                .and()
                .formLogin()
                .loginPage(this.adminServer.getContextPath() + "/login")
                .successHandler(successHandler)
                .and()
                .logout()
                .logoutUrl(this.adminServer.getContextPath() + "/logout")
                .and()
                .httpBasic()
                .and()
                .csrf()
                .csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse())
                .ignoringRequestMatchers(
                        new AntPathRequestMatcher(this.adminServer.getContextPath() +
                                "/instances", HttpMethod.POST.toString()),
                        new AntPathRequestMatcher(this.adminServer.getContextPath() +
                                "/instances/*", HttpMethod.DELETE.toString()),
                        new AntPathRequestMatcher(this.adminServer.getContextPath() + "/actuator/**"))
                .and()
                .rememberMe()
                .key(UUID.randomUUID().toString())
                .tokenValiditySeconds(1209600);
    }
}
```

서버의 설정은 이렇게 해두고 [localhost:8080/applications](http://localhost:8080/applications%EB%A1%9C) 로 접속해보면 현재 0개의 애플리케이션을 보여주고 있다고 표시된다. 그렇기 때문에 클라이언트 사이드에서도 추가를 해줘야한다

admin페이지를 통해서 관리가 필요하다고 생각이 되는 웹 애플리케이션이 클라이언트가 되는 것이다

클라이언트 측에서는 application.properties에 필요한 값들 몇가지만 해주면 관리할 수 있음

```java
## 클라이언트의 주소는 서버와의 주소가 달라야 하겠지요?
server.port=8089
## 서버의 주소
spring.boot.admin.client.url=http://localhost:8080
spring.boot.admin.client.username=admin
spring.boot.admin.client.password=admin
spring.boot.admin.client.instance.metadata.user.name=${서버에서의 네임}
spring.boot.admin.client.instance.metadata.user.password=${서버에서의 비밀번호}
```

앞으로 사용하면서 더 추가적으로 사용하는게 있다면 기록하자!

참고

[https://www.baeldung.com/spring-boot-admin](https://www.baeldung.com/spring-boot-admin)

[https://otrodevym.tistory.com/entry/spring-boot-설정하기-12-adminsecurity-설정-및-테스트-소스](https://otrodevym.tistory.com/entry/spring-boot-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0-12-adminsecurity-%EC%84%A4%EC%A0%95-%EB%B0%8F-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%86%8C%EC%8A%A4)
