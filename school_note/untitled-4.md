# Untitled

## RESTful API란? <a id="RESTfulAPI&#xC815;&#xB9AC;-RESTfulAPI&#xB780;?"></a>

REpresentational State Transfer의 약어로써, HTTP를 활용해서 데이터에 대한 접근, 데이터를 다루는 API의 아키텍처 스타일이라고 볼 수 있다.

## 왜? <a id="RESTfulAPI&#xC815;&#xB9AC;-&#xC65C;?"></a>

* 애플리케이션 분리 및 통합
* 이해하기 쉽고 사용하기 쉬운 API을 만들기 위해서
* 이해관계에 있어서 코스트를 줄이기 위해서

## 특징? <a id="RESTfulAPI&#xC815;&#xB9AC;-&#xD2B9;&#xC9D5;?"></a>

* 다양한 UI의 사용 : 자원은 단일 URL을 통해서 고유하게 식별할 수 있어야 하며, HTTP와 같은 네트워크 프로토콜의 기본 방법을 사용해야만 자원을 조작할 수 있다. 또한 HTTP 표준에만 맞는다면 어떠한 플랫폼에서도 사용이 가능
* 클라이언트 - 서버 기반 : 클라이언트와 서버 사이는 명확하게 구분되어야 한다. UI, request 수집의 문제는 클라이언트의 영역이고, 데이터의 접근, 보안 등은 서버의 영역이다. 이렇게 느슨하게 결합되어서 서로 독립적으로 개발 되어야 한다.
* stateless operations : 클라이언트와 서버는 상태를 유지할 수 없어야 한다.  필요한 상태 관리는 클라이언트에서 수행되어야 한다.
* RESTful 자원 캐싱 : 모든 자원에 대해서 캐시를 허용해 줘야 한다. 그래야 상태를 유지하지 않더라도 저장하는 것이 가능하다.
  * 브라우저는 모든 GET 요청을 캐시 가능하게 처리
  * POST는 기본적으로 캐시 처리할 수 없지만, http header에 명시시, 가능하도록 처리
  * PUT, DELETE는 캐시 처리 불가능
* Layered system : REST는 다수의 계층 구조를 가진 서버를 허용한다.
* code on demand : 대부분 서버는 정적인 자원을 보내주지만, 필요한 경우에는 실행 가능한 코드를 전달해주기도 한다. 이 의미는 클라이언트에서 실행이 가능한 코드를 서버에서 보내준다는 의미이다.

## 구성요소? <a id="RESTfulAPI&#xC815;&#xB9AC;-&#xAD6C;&#xC131;&#xC694;&#xC18C;?"></a>

* resource : URL
* method : HTTP Method
* representation of resource : HTML, JSON, XML 등

= URI을 통해서 resource을 표시해주고, HTTP Method을 사용해서 resource의 method을 정해준다. 클라이언트는 representation이 가능한 목록을 던져주고, 서버가 가능한 것을 선택한다.

## 규칙? <a id="RESTfulAPI&#xC815;&#xB9AC;-&#xADDC;&#xCE59;?"></a>

1. 소문자만을 사용한다\(대문자와 혼용하지 않는다\)
2. 언더바\( \_ \) 대신 하이픈\( - \)을 사용한다.
3. 마지막에 슬래시\( / \)을 사용하지 않는다\( / 을 통해서 계층 구조를 나타내기 때문이다\).
4. 행위\(get, post 등\)을 포함시키지 않는다\( 그런것들은 http method로, ex - getUser 이렇게 x\)
   1. Create : POST
   2. Read : GET
   3. Update 
      1. PUT : 모든 요소들에 대한 값들을 넣어줘서 보내줘야 한다.
      2. PATCH : 변경하고자 하는 요소만 넣어줘서 보내줘야 한다.
   4. Deleter : DELETE
5. 파일 확장자는 포함시키지 않는다\(Accept header를 사용하자\) -  Accept header는 request을 보낼 때 서버에게 이러한 타입으로 보내달라고 요청할 때 사용
6. 가급적으로 resource의 명사를 사용한다\(동사는 최대한 피하자\)

## 내가 만든 API가 RESTful 한지 확인 <a id="RESTfulAPI&#xC815;&#xB9AC;-&#xB0B4;&#xAC00;&#xB9CC;&#xB4E0;API&#xAC00;RESTful&#xD55C;&#xC9C0;&#xD655;&#xC778;"></a>

참고 [https://dzone.com/articles/five-clues-your-api-isnt](https://dzone.com/articles/five-clues-your-api-isnt) 

1. 엔드포인트가 단일 엔드 포인트인가?
   1. [http://localhost:8080/example](http://localhost:8080/example) ← X
   2. [http://localhost:8080/example/2](http://localhost:8080/example/2) ← O
2. 모든 request가 POST방식인가?
3. URL에 resource의 행위\(method\)가 있는가?
   1. [http://localhost:8080/item/3/active](http://localhost:8080/item/3/active) ← X
   2. [http://localhost:8080/item/23/](http://localhost:8080/item/23/)disactive ← X
4. URL에 메소드 이름이 포함되어 있는가?

다 해당하지 않는다면 RESTful 하다고 할 수 있다.

#### URI vs URL <a id="RESTfulAPI&#xC815;&#xB9AC;-URIvsURL"></a>

둘 다 Uniform Resource 이고, I는 Identifier, L은 Locator로 나누어질 수 있다.

기본정의

URI은 통합 자원 **식별자\(ID\)**로 유일하게 독립적으로 자원을 나타내는 지시자이다. 

URL은 URI의 범위 내에 있는 개념이라고 생각할 수 있다.

URL은 자원의 위치, Path을 의미한다. 그리고 일반적으로 사이트 도메인을 의미한다.

예시로 들면 

[https://localhost:8080/index.html](https://localhost:8080/index.html) 

이렇게 자원의 위치를 나타내는 것은 URL이라고 할 수 있으며, URI 이라고 할 수도 있다.

만약

[https://localhost:8080/user/132](https://localhost:8080/user/132)

이런 경우에는 user까지는 url이라고 할 수 있지만, 132같은 경우는 그냥 132번째 user를 나타내는 식별자이기 때문에 전체로는 uri이라고 할 수 있다.

