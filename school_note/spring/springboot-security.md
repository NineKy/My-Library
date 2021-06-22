# SpringBoot Security



우리가 원하는대로 유연하게 커스텀하는 것이 가능하다!

설정하기 위해서는 pom.xml에서 spring-boot-starter-security 라는 의존성을 추가해줘야한다

* AuthenticationManager bean을 등록해준다. 그러면 default로 메모리에 하나의 사용자를 등록시켜준다\(user라는 이름, pw는 콘솔창에 뜨게 된다\)
* static resource은 자동으로 무시한다 = 보안이 필요 없다
* 다양한 특징들을 활성화시켜준다. - XSS, CSRF 등등

저렇게 pom.xml에 추가만 해줘도 자동으로 인증절차를 밟아야만 웹페이지에 접근할 수 있도록 바뀌게된다 ⇒ default로 만들어진 user/콘솔에있는 비밀번호 이걸 가지고 로그인을 하면 정상적으로 접속하는것이 가능하다

만약 다른 사용자를 넣어주고 싶다면 application.properties에서

[spring.security.user.name](http://spring.security.user.name/) = 사용자이름

spring.security.user.password = 사용자비밀번호

spring.security.user.roles = USER, ADMIN

* 이거같은 경우에는 굳이 ROLE\_USER이렇게 안해도 괜찮은게 어짜피 자동적으로 앞에 ROLE\_ 이 붙게 되어있다!

하지만 이런 설정들은 따로 db에 설정해주지 않았을경우에 따로 만들어준 경우이다

WebMvcConfigure라는 interface를 implements한 WebConfig라는 클래스를 만들고, addViewControllers을 오버라이딩 해줘야한다. 안에는 ViewControllerRegistry registry.addViewController\("/"\).setViewName\("home"\);

#### Spring Security with thymeleaf

pox.xml에 thymleaf-extras-springsecurity5 추가!

이렇게 해주면 일반 컨트롤러에서 RequestMapping\("/"\)에다가 return "home"하는 함수와 같은 역할을 해준다 = 코드가 줄어든다!

SpringSecurityDialect을 bean으로 등록시켜줘야함

* SpringSecurityDialect란? thymeleaf의 extra module이고, Spring MVC와 thymeleaf을 통합해주는역할을 한다.
* thymeleaf에서 권한이 있을때를 확인하는 방법은
  * sec:authorize = "hasRole\('USER'\)" : 권한이 통과되면 출력해주는
  * sec:authentication="이름이나 권한" : 이름이나 권한을 출력해주는

#### Configure Spring Security

1. Role-based access control using a database
2. UserDetailsService to get UserDetails from database → loadUserByUsername\(\)을 통해서 service에 있는 값들을 UserDetails에다가 넣어준다!
3. Customized Spring Security Configuration

pom.xml에다가 spring-boot-starter-data-jpa을 넣어주고, mysql-connector-java 을 넣어줘야함

application.properties에다가 DataSource에 대한 세팅을 해주고, JPA에 대한 세팅을 해주고, 로깅도 해주고

#### UserDetailsService

1. 사용자의 request가 오게되면 AuthenticationFilter를 거친다
2. AuthenticationFilter를 거치면서 생긴 토큰을 AuthenticationManager에게 건내주고
3. AuthenticationManager는 AuthenticationProvider\(db와 연관되어있는\)에게 건내게된다.
4. AuthenticationProvider는 PasswordEncoder와 UserDetailsService와 연관이 되어있다.
   * 사용자가 로그인했을때 PasswordEncoder를 거치면, 해쉬된 값을 나오게된다
     * 여기서 PasswordEncoder를 빈으로 등록을 해주고, 그 안에서 BCryptPasswordEncoder\(\)을 사용한다! ⇒ 밑에 Spring Security Configuration 설정시
   * UserDetailService에는 loadUserByUserName\(\)이 있어서 loadUserByUserName\(\)이 DB를 거쳐서 UserDetails를 가지고 온다

* AuthenticationProvider가 UserDetailService를 사용하고 있고, UserDetailsService가 loadUserByUserName\(\)을 사용해서 데이터베이스에서 객체를 꺼내온다.
* 여기서 UserDetailService를 마음대로 커스터마이즈해서 활용하면 된다!

#### Spring Security Configuration

@Configuration, @EnableWebSecurity이 들어가고

WebSecurityConfigurerAdapter를 상속받는 클래스를 만든다.

만든 클래스는 Spring Security에 관한 설정들이 들어가는 클래스이다

전에 만들었던 커스텀 UserDetailsService를 @Autowired로 가져와주고, PasswordEncoder\(BCryptPasswordEncoder\)을 빈으로 등록해주고,

configureGlobal\(AuthenticationManagerBuilder auth\)로 위의 UserDetailsService와 PasswordEncoder를 등록해준다

#### BCryptPasswordEncoder

random number인 salt을 이용해서 hash password을 만들어 낸다.

$기준으로 나누고

2a : BCrypt 알고리즘 버전

10 : 알고리즘의 강도

16글자 : 랜덤으로 만들어진 16it의 salt번호

남은것 : plain text\(31글자\)

plaintext는 PasswordEncoder encoder.encode\("원하는문자"\)

이렇게 넣어서 만드러냄

#### Other Authorization

configure라는 클래스안에 매개변수로 HttpSecurity http로 선언하고

* http.authorizeRequests\(\)
  * .antMatchers\("url"\).permitAll\(\)
  * .antMatchers\("url"\).hasRole\("ROLE"\)
  * .anyRequest\(\).authenticated\(\)
  * .and\(\)로 다음껄로 이어줌
* http.formLogin\(\)
  * .loginPage\(""\)
  * .defaultSuccessUrl\(""\)
  * .failureUrl\(""\)
  * .permitAll\(\)
  * .and\(\)로 다음껄로 이어줌
* logout\(\)
  * logoutRequestMatcher\(\)
  * .logoutSuccessUrl\(\)
  * .permitAll\(\)
  * .and\(\)

#### Create View Thymeleaf

sec:authorize = "isAuthenticated\(\)"

* 만약 인증된 사용자가 접근시, 표시해줌

sec:authorize = "isAnonymous\(\)"

* 인증되지 않은 사용자가 접근시 표시

sec:authorize = "hasRole\('ROLE\_USER'\)"

* ROLE\_USER라는 권한을 가진 사람에게 표시

#### CSRF

CSRF을 방지하기 위해서는 기본으로 enable\(\)되어있기 때문에 만약에 테스트를 위해서 열어보고싶다면 HttpSecurity http.csrf\(\).disable\(\); 을 통해서 열수 잇음

CSRF token을 request시 함께 보내서 악의적인 공격이 아님을 알려야하는데, 원래 자동으로 form태그에 들어가있게된다

#### SignUp Page을 만들어보자

만들어야하는 것들

* RegistrationService Interface
* RegistrationServiceImpl Class
* RegistrationController Class
* Signup.html

