---
description: nodejs api server with sequelize
---

# a-ha 실습

### [https://medium.com/@final.lee ](https://medium.com/@final.lee
)

### 1장

npm\(Node Package Manager\)

ㄴNPMSearch에서 탐색 가능한 Nodejs.js 패키지/모듈 저장소

ㄴNode.js 패키지 설치 및, 버전/호완성 관리를 할 수 있는 커맨드라인 유틸리티

express를 설치하고 package.json에서 보면 script부분이 있다\(npm \*\*\* 이렇게 사용할 수 있게하는 명령어같은 느낌이라고 추정\)

ㄴexpress는 웹서비스를 만드는데 필요한 기능을 제공하는 프레임워크

npm start으로 디폴트인 3000번포트\(localhost:3000\)에서 접속하는 것이 가능하게 됨



### 2장

1장에서 npm start로 실행할때 start가 scripts에서 확인할 수 있었다. 좀 더 자세하게 확인해보면 주소가 있는데 그 주소는, bin폴더 밑에, 

www\(express 서버를 실행하는 코드\)에서 확인 해봐야 한다. —&gt; 여기서 포트 설정이 3000으로 되어있기 때문에 디폴트값이 3000이라고 말한 것이다.

dotenv 모듈 \(npm i dotenv\) -&gt; 환경변수\(특정process를 위한 key-value 형태의 변수\)를 사용하기 위해서 설치하는 모듈

설치한 다음에 .env 파일에서 환경변수들을 생성함

사용방법은 원하는 파일의 맨 윗 줄에 require\(‘dotenv’\).config\(\); 를 작성하면 사용할 수 있다.

app.js\(express 서버가 어떻게 동작해야 하는지를 규정한 앱 코드\)

routes/ —&gt; 라우팅을 위한 폴더이고, 클라이언트에서 요청 별로 어떠한 로직을 수행할지 정해 놓은 파일이라고 생각

ㄴ여기서 REST API가 목적이기 때문에 render함수가 아니라 json으로 변경이 필요

\*json\(JavaScript Object Notation\)은 키-값 이렇게 한쌍을 이룬 데이터를 우리가 읽을 수 있게 해주는 데이터 포맷

\*render는 컴포넌트를 html로 바꾸어 브라우저에 나타나게 하는 함수임



### 3장

Express미들웨어 

ㄴ-&gt; req, res, next으로 이루어진 함수

ㄴ-&gt; 클라이언트의 요청을 처리하여 응답하는 과정사이에 거쳐가는 함수

ㄴ-&gt; 순차적으로 실행되는 것이 특징임으로 순서가 아주 중요하다

라우터미들웨어\(애플리케이션 레벨 미들워어, 라우터 레벨 미들웨어, 오류 처리 미들웨어, 써드파티 미들웨어\)

ㄴ라우터 레벨 미들웨어: express.Router\(\)에 바인드되는 방식임-app.js에서 app.user\(\)으로 라우터파일들을 바인딩시키는 방식임

controllers폴더-&gt; 요청을 처리하는 곳임

babel

ㄴnodejs에서는 import같은 구문을 해석할 수 없기 때문에 nodejs가 해석할 수 있게 해주는 모듈

.babelrc 파일을 생성해줘야함 -&gt; balbel을 설정하기 위한 파일임



### 4장

전에 했던 babel은 babel-cli을 통해서 트랜스파일 후, 실행해야함 = 소스관리를 위한 src폴더와 트랜스파일된 실행소스를 관리를 위한 dist폴더를 분리 해야함

\*트랜스파일: 다른 실행 환경에서도 돌아갈 수 있도록 같은 언어를 유지한 채, 소스 코드의 형태만 바꾸는 과정임

라우트 분리: 모듈별, 버전별 라우트를 나눠주는 작업이 나중에 REST API버전이 올라갈때 필요하게 됨

\*라우팅: url, get, post등 특정한 엔드포인트에 대한 클라이언트 요청에 애플리케이션이 응답하는 방법을 결정하는 것을 말함

ㄴusers.route.js에서 get를 보냈고, user.controller.js에서 get에 대한 핸들러로 메세지를 보낸후, localhost:3000/v1/get 으로 컨트롤러에 있는 메세지를 postman에서 확인하는 것이 가능하다

컨트롤러: express에는 컨트롤러의 개념이 없다-&gt; MVC프레임워크가 아니라 미들웨어 프레임워크이다

\*MVC\(Model-백그라운드에서 동작하는 로직을 처리, View-사용자가 보게될 결과 화면을 출력, Controller-사용자의 입력처리와 흐름 제어를 담당\)

에러 헨들러: Nodejs는 에러가 발생하면 프로세스 자체가 죽어버리는 약한 친구이기 때문에, 에러 핸들링이 아주 중요하다 하지만 express-cli로 시작한 이상, 이미 에러 핸들링을 어느정도 해주기 때문에, 크게 신경쓸 필요는 없다



### 5장

express로 DB에 연결하는과정 —&gt; 여기에서 사용하는 것이 mysql2 와 sequelize 패키지

ORM\(Object Relational Mapping\)이란? ——&gt;객체와 관계형 데이터베이스의 데이터를 자동으로 매핑해주는 것

ㄴ객체 지향 프로그래밍은 클래스를 사용하고, 관계형 데이터베이스는 테이블을 사용함=객체단위로 쿼리를 생성해두고, 이 객체를 만들어내는 쿼리를 사용하는 방식

ㄴ데이터베이스의 데이터와 객체 필드를 매핑 = 객체를 통해 간접적으로 데이터베이스를 다룸

—장점: 재사용 및 유지보수의 편리성이 증가함, DBMS에 대한 종속성이 줄어듬

—단점: 각 플랫폼 별 ORM의 추상화된 질의어를 새롭게 배워야함

sequelize 

설치 시 + sequelize-cli도 설치해야하는데 이 패키지는 cli상에서 sequelize명령어를 사용할 수 있게 해주는 여러가지 편의기능을 제공해줌

sequelize설치 추, sequelize init 명령어로 sequelize-cli을 실행하면 자동으로 models폴더와 index.js가 생성됨

설정단계

ㄴ .sequelizerc 은 src폴더 하위에 생성해야함, 이는 sequelize를 환경 별로 다른 DB를 사용할 수 있게 유동적으로 설정하기 위함

ㄴ .env는 연결할 DB의 내용들\(루트아이디, 루트 비밀번호, 호스트\(localhost=127.0.0.1\)\)이것도 .sequelizerc와 같은 선상에 둬야함

sequelize-migration : DB스키마를 실제로 코드 베이스로 구현하기 위해서 사용함

ㄴsequelize migration:generate --name create-users / generate 명령을 통해서 테이블을 생성

ㄴsequelize db:migrate / db:migrate으로 migrate를 시행하는 부분

ㄴsequelize db:migrate:undo +:all / undo는 시행했던 migrate를 하기 전으로 되돌리는 부분이고, :all 을 붙혀주면 테이블이 생성되기 전으로 돌아가게됨

sequelize-seed : 초기 데이터 값을 코드로 미리 생성해주는 기능임

ㄴsequelize seed:generate --name seed-users 

———sequelize에서 migrate, seed 사용할때 generate되면 타임스탬프가 찍힌 파일이 생성되게 된다.내용으로는 up과 down이 있다. 

———up은 마이그레이션을 담당한다 = 새로운 컬럼을 추가하는 코드

———down은 롤백을 담당한다 = 새롭게 추가한 컬럼을 삭제하는 코드

이렇게 DB에 정상적으로 테이블을 생성했고, 데이터까지 안에 집어넣는거에 성공했으면 

조회하는 기능을 위한 models파일을 생성 -&gt; models/index.js에서 자동으로 호출해서 불림 \*users테이블과 동기화되어있어야함



### 6장

UUID란?  ——&gt; 범용 고유 식별자

ㄴ 네트워크 상에서 서로 모르는 객체들을 식별하고 구별하기 위해 각각의 고유한 이름을 부여하기 위해 고안된 기술임

ㄴ UUID4는 타임스탬프를 기반으로 생성되는 방식이고 가장 많이 사용함

uuid는 randomness를 보장받기 때문에 첫 번째 데이터와 두번째 데이터의 uuid가 문자열 정렬기준으로 뒤에오는게 아님 + 치환이 어려운 ‘-‘가 있음

===&gt;따라서 원래 uuid의 값은 string이지만, DB에서는 string 데이터를 인덱싱하는 것이 비추임

여기서 어느정도 보장받는 방법이 있음

1-2-3-4-5 ==&gt; 32145 

-로 끊어서 변경해서 사용하면 auto-increment값과 비슷한 수준의 인덱싱 성능이 가능함!

Jest란? ——&gt; 유명하고 많이 쓰이는 테스팅 프레임워크

테스트를 기반으로 한 개발이 아주 중요함! 테스트가 중요하다

TDD\(Test Driven Development\) 테스트 주도 개발로 테스트가 개발을 이끌어 간다

ㄴ테스트를 먼저 만들고 테스트를 통과하기 위한 것을 짜는 것임

ㄴ만드는 과정에서 우선 테스트를 작성하고, 그걸 통과하는 코드를 만들고를 반복하면서 계속하는과정



### 7장

migration에 대해서 추가지식:[https://victorydntmd.tistory.com/27](https://victorydntmd.tistory.com/27)

migration의 generate uuid를 추가하는데 디폴트값으로 uuid\(\)로 생긴 hex문자열을 byte buffer로 변환시켜서 넣음 === mysql에서 unhex\(\)하는 것과 같음

up부분 : addColumn{ 테이블, 컬럼이름{속성들-allowNull, unique, type, after등\)}

down부분 : removeColumn\(테이블, 컬럼\)

내용을 수정해준 다음, model파일을 models폴더안에 생성을 해주게 되고, 여기 안에있는 index.js가 반복문을 돌면서 그 안에 있는 파일들을 읽어들여 모델을을 취합

models부분으로 가게 되는데 원래 sequelize.define\(객체이름, 테이블 컬럼들, 테이블 설정\) 순으로 설정을해주고 

ㄴ테이블 설정부분에서 tableName: 부분에 써둔 이름이 DB에서의 이름이 정의되게 되고, 객체이름부분에 들어간 이름을가진 객체로 매핑이됨

migration을 완료한 상태에서 내용을 수정하기 위한 과정

ㄴ1. undo로 모든 migration파일에 대해서 down이 실행되고

ㄴ2. migration파일과 model파일의 내용을 일치시켜줌

ㄴ3.다시 migrate해주면 migration이 create되면서 내용이 일치됨

ㄴ”test": "NODE\_ENV=test npm run migrate:reset && NODE\_ENV=test npm run migrate && NODE\_ENV=test . node\_modules/.bin/jest tests --coverage" 이것도 결국은 test를 계속할때 계속해서 최신화해주기 위한 방법임\(package.json\)

supertest: 통합테스팅 모듈이라고 생각

test파일을 작성

Jest의 함수들

-beforeAll\(\), afterAll\(\): 맨 처음과 맨 마지막에 딱 한 번씩만 호출되는 함수

ㄴ테스트함수마다 매번 db와 connection을 하는 것보다 맨 처음에 한 번 connection을 해두고, 여러 함수가 사용하고 마지막에 connection을 끊음

-describe\(\): 많은 테스트함수가 있을 때, 테스트 함수들끼리 묶는 방식인 함수임

-Matcher함수들

ㄴtoEqual\(\) -&gt; 객체를 비교할때

ㄴtoBe\(\) -&gt; 기본형 값을 비교할때 

ㄴtoBeTruthy\(\), toBeFalsy\(\) -&gt; toBeTruthy\(\)는 true로 간주되면 테스트가 통과되고, toBeFalsy\(\)는 false로 간주되면 테스트가 통과됨

export{models} =&gt; export default models / import {models} from ../~ =&gt; import models from ../~

default export는 import 시, 아무 이름으로나 import가 가능하게 해준다 / var, let, const는 사용불가능함

named export는 import 시, {}안에다가 export된 이름과 동일하게 해줘야한다—만약 다른이름사용하고 싶으면 as 를 사용

의문점 : 5장에서 create으로 테이블을 만들고 시딩을 통해서 restapi\_dev라는 데베에 넣었는데, add으로 uuid컬럼을 넣고 암호화를 통해서 암호화를 해둔것을 확인해보니까 restapi\_test라는 데베에 추가되있는 것을 확인할 수 있었음

 

### 8장

JWT\(JSON Web Token\) 이란? -&gt; 사용자를 인증하는 방식

https://blog.outsider.ne.kr/1160

ㄴ크게 세 부분으로 나뉨 &gt; JOSE 헤더, JWT Claim Set, Signature

구현을 위해서 auth.route를 생성\(controller에서 login정보를 받아오는\), auth.controller는 로그인의 로직, auth.test로 테스팅

bcrypt-&gt; compare\(arg1, arg2\) 평문이 암호화된 해시와 일치하는지 확인하는 함수



### 9장

Auth: JWT

:JWT는 암호화된 토큰이자, 곧 정보를 담고 있는 문자열

:토큰에 대한 기본정보, 전달할 정보, 토큰이 검증되었다는 것을 증명해주는 signature 를 포함하고있음

JWT는 .으로 나뉘는 3개의 문자열로 구성되어있음\(헤더.내용.서명\)

헤더: typ\(토큰의 타입\), alg\(해싱 알고리즘\)

내용: Payload는 토큰에 담을 정보가 들어있음, 여기에 담는 정보의 한조각을 claim이라고 부르고 key/value의 한쌍으로 구성되어있음

registered claim:서비스에서 필요한 정보들이 아닌 토큰에 대한 정보들을 담기 위해서 이름이 이미 정해진 claim

public claim: 충돌이 방지된 이름을 가지고 있어야함-&gt;충돌방지를 위해서 uri형식으로 이름을 지음

private claim: 서비스에 필요한 정보가 들어감 -&gt; 회원정보로따지면 유저id, 유져 pw, 유저 nickname등

서명: 헤더의 인코딩값, 정보의 인코딩값을 합친 후 주어진 비밀키로 해쉬해서 생성됨\(hex-&gt;base64 형태로\)

이건 토큰 안에 정보를 온전하게 담을 수 있기에, 쓸데없는 서버요청을 줄일수있음

secret: 발급과 검증을 위해 사용되는 비밀키

param1: payload\(유저정보\)

expiresIn: 토큰의 유효기간

이렇게 JWT를 이용하면, JWT\_SECRET을 노출시키지 않고, API 서버와 통신하지 않고 토큰이 누구꺼인지 확인가능

API\(Application Programming Interface\)란

:응용프로그램에서 사용할 수 있도록, 운영체제나 프로그래밍 언어가 제공하는 기능을 제어할 수 있게 만든 인터페이스

:UI와 비슷한 개념 

ㄴUI: 사용자가 사용자와 다룰 대상을 연결

ㄴAPI: 프로그램과 또 다른 프로그램을 연결해주는 일종의 다리

:우리가 만든 프로그램이 Daum의 지도정보를 사용할 수 있도록 연결해주는 개념

REST\(Representational State Transfer\)란

:HTTP를 통해  CRUD를 실행하는 API를 의미함

조건—&gt; 1. 클라이언트-서버 구조\(resource가 있는 쪽이 서버, 요청하는쪽이 클라이언트\) 2. 무상태\(서버는 각 요청들을 별개의 것으로 가정\) 3. 캐시 처리기능 4. 계층화 5. Code on demand 6. 인터페이스 일괄성

=내가만든 서비스가 여러 기능, 프로그램을 사용할 수 있게끔 도와주는 중간 매개체이다

bearer방식이란: API에 접속하기 위해서는 access token을 API 서버에 제출해서 인증하는데, 여기서 사용하는 인증방식임

사용자를 조회하는 방식

:컨트롤러에 req.headers.authorization이 있으면 token의 정보를 받아오고

:토큰에서 payload를 뽑아오고, 그걸로 db에 사용자 쿼리를 날리고

:사용자 정보를 받아온다.



### 10장

Cache

ㄴ 서비스가 커짐에 따라, 트래픽이 커지게 되고 이는 DB의 I/O의 부하가 걸림 

ㄴ-&gt; 서비스가 커지기 전에 cache설계를 해두는게 도움이 잘됨

Redis모듈을 사용

redis는 in memory database로서, read의 성능이 보장받는다

Redis\(REmote Dictionary Server\)란?

:메모리 기반의 ‘키-값’ 구조 데이터 관리 시스템이며, 모든 데이터를 메모리에 저장하고 조회하기에 빠른 Read, Write속도를 보여주는 비 관계형 데이터베이스임

특징 —&gt; 영속성을 지원하는 인메모리 데이터 저장소, 읽기 성능 증대를 위한 서버 측 복제를 지원

장점 —&gt; 리스트, 배열과 같은 데이터를 처리하는데 유용함, 리스트형 데이터 입력과 삭제가 MySQL에 비해서 아주빠름, 메모리를 활용하면서 영속적인 데이터 보존, 싱글스레드로 수행되서 하나의 서버에서 여러개의 서버를 띄우는 것이 가능함

캐싱할떄, 변경 가능한 정보등을 캐싱 안하는게 좋나

-&gt;Redis를 사용할때는 I/O의 성능이 굉장히빠르게 update사항이 생기는대로 redis에 바로 적용해도 괜찮다. 실제로도 실시간으로 많은 데이터를 읽고써야하는 프로그램에서는 1차 DB로 redis를 사용중임

-&gt;afterSave\(\) 훅을잡아서 redis에 적용함 =&gt; 모델 구현체에 hook을 잡게되면 매번 모델이 update되었는지, update가 됬으면 redis에 객체가 있는지, 있으면 update해야하는지 고민을 안해도 됨

redis를 쓸떄, redis에서 읽기작업시 실패했을때 RDB로 접근 해서 정보를 가져오는 방식으로 로직을 구현하는게 서비스 제공 관점에서 좋은데 다른 대안 -&gt; redis는 휘발성이고, 일정 데이터가 쌓이면 자동으로 데이터를 삭제하는데 ㄱㅊ?

-&gt;in memory db이니까 휘발성 db는 맞음, 하지만 캐시를 날리지 않게 도와주는 persistance 모듈이 있기때문에 걱정안해도 ㅇㅋ



### 11장

cache layer의 수정

Hset : key에 저장된 해시 필드를 설정

Hget: key필드에 저장된 값을 불러옴

repository에서 crud작업을 도맡아서 하는중

캐시를 쓰게되면 repository에서 가져와야하는지 cache에서 가져와야하는지 모르게되니까 repository에서 가져오는 방식으로….



### 12장

\*OSI 7계층

7: Application layer\(응용 계층\) -&gt; 응용 서비스를 네트워크에 접속시키는 역활

6: Presentation layer\(표현 계층\) -&gt; 송신측과 수신축 사이에서 표준화된 데이터 형식을 규정

5: Session layer\(세션 계층\) -&gt; 통신에 대한 제어구조를 제공하기위해 응용계층 사이의 접속을 설정, 유지, 종류

4: Transport layer\(전송 계층\) -&gt; 프로토콜과 관련된 계층으로, 오류 복구, 흐름제어 담당하고 신뢰성있는 데이터전송을 보장

3: Network layer\(네트워크 계층\) -&gt; 패킷을 송신측으로부터 수신측으로 전송

2: Data Link layer\(링크 계층\) -&gt; 물리적 링크를 통해 데이터를 신뢰성 있게 전송하는 계층, 전송로 역활

1: Physical layer\(물리 계층\) -&gt; 데이터 전송을 위해 링크를 활성화하고 관리하기 위한 기계적, 전기적, 절차적특성



### 13장

로그의 구현은 필수이다. 

-&gt; morgan, winston 모듈

morgan: req, res를 formatting 해주는 모듈 -&gt; 여러가지 preset들 중에서 combined, comm에 정보를 추가해서 사용하는게 좋음

winston: forrmatting된 로그를 json 형식으로 기록해주는 모듈



### 14장

sentry, rollbar

sentry-&gt; 로그 취합, 분석 및 트래킹 시스템임

DSN는 sentry 페이지에서 검색 가능

이걸로 이메일을 에러에 대한 이메일을 받을 수 있음-&gt;하지만 부족하니까 슬랙으로 알림을 받는 것도 가능

@slack/client 모듈로 slack과 연동하면 알림까지 받을 수 있음

pm2를 이용해서 서버에 직접 배포

docker를 입혀서 이미지를 올리는 것

7.12

//마이그레이션은 데이터베이스 스키마에 변경이 필요할 떄마다 마이그레이션 코드를 만들어서 진행되고, 명령어 실행시 migrations폴더에 타임스탬프가 찍힌 파일이 생성됨

//up은 마이그레이션을 담당: = 새로운 컬럼을 추가하는 코드같은

//down은 롤백을 담당: =추가한 컬럼을 삭제하는 코드같은

tests/functions/integrations/v1/users.test.js

//expect\(response.body.email\)

//    .toBe\(user.email\)

// Object.is equality에러, toBe\(\)는 기본형 값을 비교할 때 사용함

//expect\(response.body.email\).toEqual\(user.email\)-&gt;deep equality에러

//toEqual\(\)은 객체를 검증할때 자 주  사용하는 함수임

//Object.is\(\)는 value1과 value2 두 값이 같은 값인지 결정함

7.13

7장을 하는 도중에, 컬럼을 추가하는 상황에서 문제가 생겼어요…

5장에서 처음에 테이블을 생성시에 컬럼들을 만들어서 sequelize db:migrate으로 테이블을 생성하고, 값들을 입력한 seed파일을 sequelize db:seed:all으로 먼저 넣어주는 거까지 확인을했습니다. 

6장까지는 잘 성공했었지만

7장 처음에 generate으로 파일을 생성해주고, 새로만들 uuid컬럼을 특정시킨 후, user.model에서 디폴트값을 설정해주고 test파일을 생성한 후 package.json부분까지 수정을 하고 npm test를 실행해보면 

이런 문제가 생겨서 

npm test하기 전에 user.model까지 수정을 한 후, db:migrate해주니까 이런 에러가 생깁니다.

여러가지 검색해봤는데 데이터가 들어가있는 테이블의 컬럼을 수정하는 과정은 삭제후 재 생성을 해야한다고 해서 확인해보니까 데이터가 없는 상태에서는 컬럼이 정상적으로 들어가더라구요.. 

7장의 에러는 문제가 안생기게 되었지만 남은 문제가 있다

=&gt;npm test를 실행하게되면 문제는 없다고 나오지만 DB에서는 uuid컬럼과 값들이 추가가 안되어있음

ㄴ5장에서 migrate를 했고, seed:all로 값들을 넣어두었는데, 7장에서 컬럼을 추가할때 추가가 안된다.

그래서 따로 db:migrate를 해보니까 Validation error가 발생하는 문제가 생겼다. 디폴트값설정은 users.model에서 했는데… 왜안될까요

얼핏보니까 데이터가 들어가있는 테이블의 속성값들은 변경할 수 없다고 본 거 같기도 한데….. 

8장은 이병헌의 앗까지

7.14

API는 web과 app에서 사용되는 api는 보통 서비스에서 소스는 공개하지 않고 해당 기능을 제공하려고할때 사용함

REST API-&gt;HTTP 기반으로 필요한 자원에 접근하는 방식을 정해놓은 아키텍쳐

get.의 에러처리를 해줘여한다고 하는데… 이걸 어디서 건드려봐야하는지 잘 모르겠다…. 확인해봐야함

uuid가 정상적으로 변하는 거까지는 확인했지만 문제는 mysql에는 uuid컬럼이 생기지도 않았으며, 데이터들이 변환된것을 확인하지 못했다.

Redis 설치, 사용법 메뉴얼 -&gt;[https://swiftymind.tistory.com/62](https://swiftymind.tistory.com/62)

서비스 실행: brew services start redis

서비스 중단: brew services stop redis

서비스 재시작: brew services restart redis

redis 실행: redis-server

medis설치방법임-&gt; 클론하라는 의미가 주소를 복사해서 맨처음 줄처럼 작성하는 방법이구나

git clone https://github.com/luin/medis.git

cd medis

npm i

npm run pack

\# webpack analyzer가 뜨고 난 후 프로세스를 종료하세요 \(^C\)

node bin/pack.js

\# No identity found for signing 에러는 무시하세요

cd dist/out/Medis-mas-x64/

open .

\# Finder가 열리고 Medis.app이 보일 겝니다.

medis 설치후,  캐시화 되어있는 user들의 정보를 확인해보니까 기존에 mysql에 있던 3개의 정보와 추가된 하나 더 해서 4가지가 있어야하지만 현재 추가한 하나의 정보만있음 ———&gt; 결국 migrate부분에서 작동이 문제가 생기고 있다는 의미임

결과적으로 현재까지의 문제는 2개가 있음

7장-migrate에서 uuid가 생기지 않은점 

-mysql에 이미 들어있는 정보가 암호화 되지 않은점 —&gt; 요부분은 mysql에서 가져와서 콘솔로 찍어보면 정상적으로 uuid화되어서 출력이됨=uuid의 과정은 정상적이지만 mysql에 입력이 되지 않았따

9장-npm test 했을 때 Route.get\(\) requires a callback function but got a \[object Undefined\] ——&gt;이렇게 문제가 발생하는것을 확인할 수 있음

7.15

현재, user.test.js에서 expect\(response.body.length\).toBeGreaterThan\(1\)에서 에러가 발생중이다….

**ㄴMatcher error**: received value must be a number or bigint 메세지를 보면 문제는 아마 response.body.에서 발생하고 있는듯하다, 형이 안맞는거니까 length까지는 문제될껀 없겠고… response.body에서 객체를 잘못넘기고있나 확인해봐야할꺼같음

DB를 확인해보니까 암호화하고 uuid가 추가된부분은 npm test로 실행을 해줘서 그런건지 restapi\_dev가아니라 restapi\_test에서 새로운 uuid컬럼과 내용들이 전부 암호화해서 나와있는 것을 확인할 수 있음

7.16 

SSH\(Secure Shell\)은 네트워크 상의 다른 컴퓨터에 로그인하거나 원격 시스템에서 명령을 실행하고 다른 시스템으로 파일을 복사할 수 있도록 해주는 응용프로그램/프로토콜

Shell이란 커널과 이용자 사이에 끼어서 이용자의 명령을 해석, 처리결과를 “뿌려주는 시스템 프로그램

=커널을 이용하기 위해서 이용자들이 커널의 껍데기를 통한다고 해서 쉘이라는 개념이 나옴

slack에 연결할때, Incoming Webhhok은 앱의 이름이니까 앱 검색하는 곳에서 검색하면 확인이 가능함

sentry에서 dsn을 찾을때, setting -&gt; project -&gt; client keys\(DSN\) 항목에 들어가면 DSN을 복사할 수 있게 해둠

Second 

connect ECONNREFUSED 127.0.0.1:80

ㄴ post 과정에서 url를 설정하다가 문제가 생기면 나오는현상이라네요 =&gt; 앞에 /부분에서 쓸때없이 ./으로 써놔서….

 Route.get\(\) requires a callback function but got a \[object Undefined\]

ㄴ 미들웨어를 직접 만들어서 활용하는 과정에서 get에서 콜백함수가 없다고하는데…

컨트롤러에서 함수를 만들어주면된다 -&gt; export const tokenTest = async\(req, res, next\)=&gt;{

    try{

        return response\(res, req.user\)

    }catch \(e\) {

        next\(e\)

    }

}

get tokenTest에서 토큰테스트가 값이 없다고해서 그런거니까 만들어주니까 에러가 안생김

cache의 심화부분\(11장\)에서 기존 usercache부분을 userrepo로 변경해야하는 점도 생각보다 많았다. import userrepo를 해주고, 새로운 생성자를 만들고 그 만든 생성자를 cache와 교체해주는 작업이였다.

cache를 사용하는 방법까지는 정상적으로 되었지만 그 상태로는 user repository에서 문제가 생긴다 =&gt; 하나의 함수에서 여러가지의 반환값을 가지게 되는 문제에 직면하게 된다.

+toWeb\(\)이라는 함수가 cache가 있을 때는 동작을 안하는 문제까지!

그래서 user.model에 있는 cache부분을 모두 repository로 옮겨오고 반환값을 통일시켜주는 작업을 했다.



### Github 주소

#### 첫 번째 실습

{% embed url="https://github.com/kyu9/aha\_fir" %}

#### 두 번째 실습

[https://github.com/kyu9/aha\_sec](https://github.com/kyu9/aha_sec)

