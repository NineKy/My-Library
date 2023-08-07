# 독립 실행형 서블릿 애플리케이션

## Http의 요청과 응답

이거 전에 '스프링부트 시작하기'라고 나와있는게 있긴한데 딱히 할만한게 없어서..\
HTTP 의 req, res 에 대해서도 나와서 간단하게 정리만 하자\
HTTP 를 검색해보면 클라이언트와 서버 간 통신을 위한 통신 규칙 또는 프로토콜 이라고 나온다\
Hyper Text Transfer Protocol 의 줄인말으로 지금까지의 지식으로는 통신을 하는데 있어서의 규칙 정도로 이해하고 있다\
\


http 가 규칙이다 보니, 규칙에 맞게 해당 프로토콜에는 다양한 정보들이 존재\
REQ 에는 아래와 같은 것들이 있다

* Request Line : Method(GET, POST, PUT, etc...), Path(URI), HTTP Version(회사에서는 기본적으로 1.1)
* Headers : 말 그대로 헤더라인(공통적으로 들어가는 정보? 같은 것들을 넣기도 하고 여기에 뭐가 들어가느냐에 따라서 req의 내용이 변경되기도 함
* Message Body : 만약에 요청할 떄 어떠한 정보를 주면서 요청해야하는 경우에 필요

RES 에는 아래와 같은 것들이 있다

* Status Line : HTTP Version, Status Code(200, 400 등 지정된 숫자 링크참조 https://developer.mozilla.org/en-US/docs/Web/HTTP/Status), Status Text(Status Code의 내용)
* Headers : 위와 공통
* Message Body : 위와 공통

\
\


## Containerless

이제는 스프링부트를 만들게 된 계기라고 볼 수 있는 >> Containerless\
기본적으로 우선 스프링 이니셜라이저를 통해서 스프링부트를 만들게 되면, @SpringBootApplication 이라는 애노테이션과 함께 SpringBootApplication.run 이라는 놈을 통해서 스프링부트가 톰캣을 기반으로 서버가 기동되는 방식이다\
![img.png](img/chap2\_1.png) 기본적으로 이렇게 생겼는데, 요게 서버를 띄워주고 각 컨트롤러가 동작하도록 해주는 부분이다 그리고 이걸 직접 구현해보고자\
@SpringBootApplication 을 제거, SpringApplication.run 을 제거하고 직접 서블릿 구현을 통해서 어떻게 컨트롤러로부터 전해져서 오는지에 대해서 확인해보자\


\


## 직접 서블릿 컨테이너 만들어보기

아래와 같이 변화할 수 있다\
변경된 내용을 이러하다 >> ServletWebServer를 통해서 서버를 만들고 >> 서블릿을 추가하고 >> url을 매핑하고 >> response 값을 지정해준다

* HttpHeaders.\~\~ , MediaType.\~\~ , HttpStatus.\~\~ 이렇게 enum 으로 고정된 놈들은 이거 쓰라고 구현되어있으니까 쓰면됨
* req.getParameter 을 통해서 req에서 넘어오는 파라미터를 가져와서 사용할 수 있다 ![img.png](img/chap2\_2.png)\
  \


이렇게 해주고 해당 부트를 실행해보면 ![img\_1.png](img/chap2\_3.png) 내가 지정해준 undertow 서버로해서 서버가 정상적으로 뜬 것을 확인할 수 있고\
\
\


/hello 주소를 통해서 확인해보면 ![img\_2.png](img/chapr2\_4.png) 서블릿에서 커스텀으로 만들고 지정한 header, statusCode, responseBody가 나오는 것을 확인할 수 있다\
\
\


## 프론트 컨트롤러

기본적인 컨트롤러는 위에서 만든대로만 사용해주면 되는데 프론트, 즉 뷰를 렌더링할 때는 따로 지정?은 아니지만 뷰 전용 공통 서블릿을 통해서 들어오도록 설정한다\
뷰 서블릿 컨테이너에서는 다양한 기능들을 수행해야하는데, 뷰 서블릿에서 각 뷰의 매핑 과정도 처리해주는데,\
service() 메소드 내부에서 req.getRequestURI() 이라는 메소드를 통해서 url 을 알 수 있고 뭐 다른 헤더나 다른 방법들을 통해서 할 수도 있긴함\
오.. 뷰를 던지는 컨트롤러 같은 경우에는 서블릿 단에서 컨트롤러 클래스를 인스턴스화, 메소드에 파라미터값 넣고 리턴되는 값을 response body에 넣어주기만 하면 화면을 출력\
\
\
\


\==================================\
여기까지가 스프링 없이, 오직 서블릿만을 활용해서 서버를 띄우고, http의 req, res을 어떻게 처리하는지에 대해서 실제로 구현해보면서 확인해봄\
\==================================\
\
\


\
\
\
\
\
\
\
\
\
\
