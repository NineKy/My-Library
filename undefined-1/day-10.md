# Day 10

## 이전의 OAuth에서부터 진행

### 어노테이션 기반으로 개선

프로그래밍에서 코드의 개선이 필요할 때가 있다(나쁜코드)

대표적으로는 : 반복되는 같은 코드

우리의코드에서는 IndexController에서의 세션값을 가져오는 부분이 문제임

```java
SessionUser user = (SessionUser)httpSession.getAttribute("user");
```

index 메소드 외의 다른 컨트롤러와 메소드에서 세션값이 필요하면 그 때마다 직접 세션에서 값을 가져와야함

→ **그래서 이 부분을 메소드 인자로 세션값을 바로 받을 수 있도록 해보자**

config.auth 패키지에 @LoginUser 어노테이션을 생성하자

→자바파일에서 Annotation을 만드는 곳이 있더라

src/main/java/com/kyu/book/springboot/config/auth/LoginUser

```java
package com.kyu.book.springboot.config.auth;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
public @interface LoginUser {
}
```

* @Target(ElementType.PARAMETER)
  * 이 어노테이션이 생성될 수 있는 위치를 지정
  * PARAMETER로 지정했으니 메소드의 파라미터로 선언된 객체에서만 사용할 수 있음
  * 이 외에도 클래스 선언문에서 쓸 수 있는 TYPE 등이 있음
* @interface
  * 이 파일을 어노테이션 클래스로 지정한다
  * LoginUser라는 어노테이션이 생성되었다고 생각하면 된다

다음으로 같은 위치에 LoginUserArgumentResolver를 생성

→HandlerMethodArgumentResolver 인터페이스를 구현한 클래스

* 조건에 맞는 경우 메소드가 있다면 HandlerMethodArgumentResolver의 구현체가 지정한 값으로 해당 메소드의 파라미터를 넘길 수 있음

src/main/java/com/kyu/book/springboot/config/auth/LoginUserArgumentResolver

```java
package com.kyu.book.springboot.config.auth;

import com.kyu.book.springboot.config.auth.dto.SessionUser;
import lombok.RequiredArgsConstructor;
import org.springframework.core.MethodParameter;
import org.springframework.stereotype.Component;
import org.springframework.web.bind.support.WebDataBinderFactory;
import org.springframework.web.context.request.NativeWebRequest;
import org.springframework.web.method.support.HandlerMethodArgumentResolver;
import org.springframework.web.method.support.ModelAndViewContainer;

import javax.servlet.http.HttpSession;

@RequiredArgsConstructor
@Component
public class LoginUserArgumentResolver implements HandlerMethodArgumentResolver {

    private final HttpSession httpSession;

    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        boolean isLoginUserAnnotation = parameter.getParameterAnnotation(LoginUser.class) != null;
        boolean isUserClass = SessionUser.class.equals(parameter.getParameterType());
        
        return isLoginUserAnnotation && isUserClass;
    }

    @Override
    public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
        return httpSession.getAttribute("user");
    }
}
```

* suppertsParameter()
  * 컨트롤러 메소드의 특정 파라미터를 지원하는지 판단
  * 여기서는 파라미터에 @LoginUser 어노테이션이 붙어 있고, 파라미터 클래스 타입이 SessionUser.class인 경우 true를 반환
* resolveArgument()
  * 파라미터에 전달할 객체를 생성한다
  * 여기서는 세션에서 객체를 가져온다

—여기까지가 @LoginUser를 사용하기 위한 환경을 구성—

이제는 LoginUserArgumentResolver가 스프링에서 인식될 수 있도록 WebMvcConfigurer에 추가

config패키지의 webConfig 클래스를 생성해서 설정 추가

src/main/java/com/kyu/book/springbook/config/WebConfig

```java
package com.kyu.book.springboot.config;

import com.kyu.book.springboot.config.auth.LoginUserArgumentResolver;
import lombok.RequiredArgsConstructor;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.method.support.HandlerMethodArgumentResolver;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

import java.util.List;

@RequiredArgsConstructor
@Configuration
public class WebConfig implements WebMvcConfigurer {
    private final LoginUserArgumentResolver loginUserArgumentResolver;

    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> argumentResolvers){
        argumentResolvers.add(loginUserArgumentResolver);
    }
}
```

* HandlerMethodArgumentResolver는 항상 WebMvcConfigurer의 addArgumentResolvers()를 통해서 항상 추가해야한다.
* 다른 HandlerMethodArgumentResolver가 필요하다면 같은 방식으로 추가해주면 된다

—이걸로 모든 설정이 끝났으니까 IndexController의 코드에서 반복되는 부분을 모두 @LoginUser로 개선

src/main/java/com/kyu/book/springboot/web/dto/IndexController

```java
private final PostsService postsService;

@GetMapping("/")
public String index(Model model, @LoginUser SessionUser user){
    model.addAttribute("posts", postsService.findAllDesc());
    if(user != null){
        model.addAttribute("userName", user.getName());
    }
    return "index";
}
```

* @LoginUser Session user
  * 기존에 (User)httpSession.getAttribute("user")로 가져오던 세션정보 값을 이제 그 방식을 사용하지 않아도 가능
  * 이제는 어느 컨트롤러든지 @LoginUser만을 사용하게되면 세션의 정보를 가져와서 사용할 수 있다
