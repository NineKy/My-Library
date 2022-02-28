# Thymeleaf

### Thymeleaf란?

독립적인 환경에서 단독적으로 사용이 가능한 java template engine이다. 

+공식적으로 스프링진영에서도 밀고 있는 템플릿이다.

사실 mustache보다는 문법적으로 어려운 부분이 있지만 많이 밀고있는 템플릿이고 그 의미는 호환이 가장 잘 된다는 의미니까 배우면서 공부해보자



### Gradle에 추가

의존성을 추가해주자

```text
implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
```

+프론트를 별로 안만져서 딱히 필요성이 안느껴졌는데 필요하면 devtool 의존성을 추가하고 live refresh에 대해서 찾아보자..!



### 사용하기 전 주의할점

thymeleaf은 파일타입도 아니고 단지 문법을 사용해서 원하는 값들을 웹 페이지에서 보기 위해서 사용하는 개념이다! -&gt;**따라서 딱히 어떤 조정이 필요한 부분은 아니다!**

#### 뷰\(View\)에 해당하는 HTML 와 같은 것들은 main/resources/templates 패키지 안에 넣어둬야하는점!!

컨트롤러 부분에서 원하는 값들을 던져주게 되는데 평소에 별 생각 없이 사용하던 @RestController는 사용하면 실행이 안된다!

#### 템플릿을 반환하는 경우에는 @RestController 대신 @Controller를 사용해줘야한다!!



추가적인 문법은 다시 공부하고 진행해보는걸로하자..!









