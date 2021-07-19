# Untitled

### 기본 정의\(위키백과 참조\)

**SW의 개념으로 보면 : Client으로부터 HTTP 요청을 받아들이고, HTML와 같은 정적인 웹페이지를 응답 해주는 프로그램**

**HW의 개념으로 보면 : 위에서 언급한 SW을 직접 돌리는 컴퓨터**

### 정적 페이지 vs 동적 페이지

정적이라는 의미를 가지는 이유는 HTTP request가 웹 서버에게 전달되었을 때, 고정된 뷰, 혹은 정해진 값들을 보내주기 때문이다.

즉, 항상 동일한 결과를 보여주는 페이지이라는 의미이다.

예시로는 포트폴리오, 회사 소개 사이트 등

동적이라는 의미는 템플릿 안에서 값들이 DB와 같은 저장소에서 값들을 다르게 가져와, 변경되어서 출력된다.

즉, 간단한 템플릿에 다수의 db에서 가져온 값들로 구성된 뷰 / 항상 변화되는 값들로 구성된 뷰 / 사용자에 따라서 다른 페이지 가 동적 페이지이다.

예시로는 내 정보 페이지, 네이버 홈페이지 등

### WAS\(Web Application Server\)

HTTP 를 통해서 사용자 컴퓨터나 장치에 애플리케이션을 수행해주는 미들웨어로써, 주로 동적 컨텐츠를 수행한다.

![](https://konawiki.konai.com/download/attachments/192954653/webserver-vs-was1.png?version=1&modificationDate=1625716384000&api=v2)

WAS = Web Server + Container로 구성되어있다.

Web Server란 HTTP을 기반으로 하여 클라이언트의 request을 서비스하는 담당을 하고 있다.

그리고 가장 앞 단에서 request에 대한 데이터를 가지고 있다가 response 해주는 역할을 담당하고 있으며 정적인 컨텐츠를 중심으로 제공해주고 있다. 만약 동적인 컨텐츠가 필요하면 WAS에게 요청을 다시 해서 받고 그것을 응답해준다.

예시로는 Apache가 있다.

WAS\(Web Application Server\)는 동적인 컨텐츠를 제공하기 위해서 만들어진 애플리케이션 서버이다.

구체적으로는 DB조회나 로직 처리를 해주는 부분이다.

WAS는 JSP, Servlet 구동환경을 제공해주기 때문에 웹 컨테이너 혹은 서블릿 컨테이너 라고도 불린다.

예시로는 Tomcat이 있다.

#### Servlet?

request를 받고, 처리를 하고 response해주는 과정을 처리해주는 자바 클래스

#### Web Container?

웹 서버가 보낸 JSP, PHP 등의 파일을 수행한 결과를 다시 웹 서버로 보내주는 역할

#### Servlet Container?

위에 정의해뒀던 servlet들을 관리하는 친구이다.

request를 받고, response할 수 있도록 웹 서버와 소켓을 만들어서 통신을 도와준다.

WAS은 정적, 동적 처리 모두 가능한데 WAS만 사용해도 무관한 거 아닌가?  WAS은 DB조회와 로직에만 집중할 수 있도록 부담을 덜어주기 위해서 사용한다.

#### apache? tomcat? apache tomcat?

apache : 웹 서버에 해당한다고 볼 수 있고, 정적인 항목들을 처리한다. 

tomcat : servlet container에 해당하기 때문에 동적인 항목들을 처리해준다. 그래서 DB나 다른 데이터들을 처리, 다른 응용 프로그램들과 상호작용이 가능하다. 스프링부트에 기본적으로 내장 

apache tomcat : 정적인 항목과 동적인 항목을 함께 처리할 수 있기 때문에 분산해서 효율적으로 처리하는 것이 가능하다. 

