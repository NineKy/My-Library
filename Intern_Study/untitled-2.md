# HTTP

### HTTP? <a id="HTTP&#xD1B5;&#xC2E0;&#xC6D0;&#xB9AC;-HTTP?"></a>

HyperText Transfer Protocol의 약자이고, protocol이 들어갔다는 의미는 약속, 규약을 의미한다.

그리고 크게 request와 response 으로 주고 받는다.

그리고 OSI 7 layer에서 7층인 application layer에서 사용되는 프로토콜이다. 

+ 인터넷에서는 TCP 으로 인터넷을 거친다. 

#### OSI 7 layer <a id="HTTP&#xD1B5;&#xC2E0;&#xC6D0;&#xB9AC;-OSI7layer"></a>

1. \(Physical\)물리 계층
2. \(DataLink\)데이터링크 계층
3. \(Network\)네트워크 계층
4. \(Transfer\)전송 계층
5. \(Session\)세션 계층
6. \(Presentation\)표현 계층
7. \(Application\)응용 계층

이렇게 구성되어 있다.

다시 HTTP의 특징으로 돌아가서, HTTP는 기본적으로 stateless 를 유지하고 있으며 connectless 방식으로 작동한다.

connectless는 서버에 연결을 요청하고 요청에 대한 응답을 받으면 연결을 끊어버린다는 것이다.

stateless는 클라이언트와 서버가 통신 시, 수신자는 이전의 요청을 기억\(유지\)하지 않다는 것이다. 

즉, 각각의 통신이 독립적이기 때문에 이전의 통신과는 아무런 상관이 없다고 볼 수 있다.

이러한 특징으로 가질 수 있는 장점은

1. 안정성 : 부분적 실패에 대해서 복구하는 것이 쉬워진다
2. 확장성 : 세션 상태를 유지할 필요가 없어지기 때문에 서버의 부담이 준다

이러한 stateless을 보완하기 위해서 사용하는 것이 쿠키 / 세션이다.

### HTTP 구성  <a id="HTTP&#xD1B5;&#xC2E0;&#xC6D0;&#xB9AC;-HTTP&#xAD6C;&#xC131;"></a>

[참고 : https://velog.io/@teddybearjung/HTTP-%EA%B5%AC%EC%A1%B0-%EB%B0%8F-%ED%95%B5%EC%8B%AC-%EC%9A%94%EC%86%8C](https://velog.io/@teddybearjung/HTTP-%EA%B5%AC%EC%A1%B0-%EB%B0%8F-%ED%95%B5%EC%8B%AC-%EC%9A%94%EC%86%8C) 

#### Request = start line + headers + body <a id="HTTP&#xD1B5;&#xC2E0;&#xC6D0;&#xB9AC;-Request=startline+headers+body"></a>

**start line = http method + request target + http version**

GET /login HTTP/1.1 ← 이렇게 생김

http method : 주어진 리소스에 수행하길 원하는 행동을 의미하고, 종류로는 GET, POST, PUT, DELETE 등이 있다.

request target : 해당 request가 전송될 url

**headers** 

해당 request에 대한 추가 정보가 들어있음, key : value 형태로 구성되어 있음

**body**

해당 request의 실제 내용이 담겨있음

#### Response = status line + headers + body <a id="HTTP&#xD1B5;&#xC2E0;&#xC6D0;&#xB9AC;-Response=statusline+headers+body"></a>

**status line = http version + status code + status text**

HTTP/1.1 404 Not Found ← 이렇게 생김

status code : 응답 상태를 나타내는 코드로, 나누자면

* 1xx : 정보 / 요청을 받았으며, 프로세스를 계속한다.
  * ex\) 100\(continue\) : 클라이언트가 서버로 보낸 요청에 문제가 없으니 다음 요청을 이어서 보내도 된다는 의미
* 2xx : 성공 / 요청을 성공적으로 받았다.
* 3xx : 리다이렉션 / 요청 완료를 위해 추가 작업 조치가 필요하다.
* 4xx : 클라이언트 오류 
* 5xx : 서버 오류

**headers : 서버와 동일한 개념**

**body : 서버와는 다르게 비어있는 경우가 많다**

#### HTTP ContentType <a id="HTTP&#xD1B5;&#xC2E0;&#xC6D0;&#xB9AC;-HTTPContentType"></a>

* Application/x-www-form-urlencoded : HTML Form 형태
* Application/json : JSON 형태 값
* multipart/form-data : 파일 첨부
* text/\* : 단순 html, css, javascript 파일

### HTTP 통신 원리 <a id="HTTP&#xD1B5;&#xC2E0;&#xC6D0;&#xB9AC;-HTTP&#xD1B5;&#xC2E0;&#xC6D0;&#xB9AC;"></a>

각 기기에는 인터넷에서 서로를 구별하기 위한 고유 주소가 있다. 그리고 이 고유 주소를 IP주소라고 한다. 

그리고 이 IP주소를 매번 기억하기 어렵기 때문에 우리가 사용하는 것이 도메인 이름이다.

도메인 이름는 숫자의 조합으로 되어있는 IP주소를 암기하기 어렵기 때문에 문자 방식으로 풀어둔 것이다.

도메인 이름을 입력하면, 해당 이름에 해당하는 IP주소를 DNS\(Domain Name System\)에서 찾게 된다.

그리고 HTTP로 연결하게 되면 TCP위에서 연결되기 때문에 서버와 클라이언트 간 연결을 3-way-handshake 방식으로 연결하게 된다.

* 3-way-handshake 방식은 서로가 준비되었다는 것을 확인 과정을 거치고 통신을 진행하겠다는 의미이다.
* TCP는 연결 지향 프로토콜이기 때문에 양방향이 모두 연결되고 나서 통신을 시작하는 방식이다.
* 연결의 종료는 4-way-handshake 방식을 사용하게 된다.

그리고 나서 클라이언트에서 보냈던 request을 서버에게 보내게 되고 

서버에서 처리하고 

클라이언트에게 response을 보내준다.









