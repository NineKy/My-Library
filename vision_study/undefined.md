---
description: 'aha 실습을 2번 돌려서 공부를 진행한 다음, 혼자서 0부터 진행'
---

# 혼자서 만들어본 게시판

\*\*\*\*

\*\*\*\*

njs\_bbs라는 이름을 가진 프로젝트폴더를 생성함, cd njs\_bbs로 이동

express —view=pug njs\_bbs 

njs\_bbs라는 이름을가지고, pug를 사용하는 프로젝트 생성됨, cd ~/njs\_bbs/njs\_bbs로 이동

이렇게하면 express라는 웹프레임워크를 사용하는 프로젝트의 생성까지는 성공했고

로그인을 위해서는 회원가입폼을 만들어야함

jsp게시판의 틀을 빌려서 사용함

회원가입을 먼저 구현하기 위해서는 mysql을 연동해야하고, orm을 사용하기 위해서 sequelize를 사용

npm i sequelize mysql2

npm i sequelize-cli

sequelize init

여기까지하면 configs/configs.js 와 migration폴더가 생성된다.

configs.js는 mysql과 연동할때 필요한 아이디와 비밀번호 그리고 사용하고자하는 스키마 이름을 기록해줌

테이블을 express에서 생성해주기 위해서

models/user 에서 user의 테이블을 정의 -&gt;

app.js에서 

var sequelize = require\('./models'\).sequelize;

sequelize.sync\(\);

=npm start시, model/ 에 존재하는 테이블의 구조를 config에서 연동했던 mysql의 schema에 똑같이 동기화시켜줌\(=생성\)

* 계속 막혔던건데 시퀄라이저가 생성한 table은 define에 정의해둔 이름에다가 s를 붙혀서 테이블을 생성해줌

정상적으로 돌아가고 있는지 확인하기 위해서 route/test를 생성

findAll을 사용해서 모든 값을 가지고 오고

res.render에서 옵션으로 가지고온 값을 pug로 넘겨줌

test.pug에서는 보낸 값을 for 변수 in 넘긴값 으로 현재 테이블에 있는 모든 값들을 출력할수있음

pug는 동적으로 페이지를 만들ㅇ서 html을 만들어줌 -&gt; render를 사용하면됨

디비에서 데이터를 가져와 pug파일로 던져줄꺼고, pug파일에서 이 data를 가지

고 페이지를 만들어줄꺼임

DB의 구성을 짰고, 그 구성대로 모델을 만들어 bbs에 users, posts, comments를 만들었다.

모델의 관계 표현해줘야함 -&gt; models//index.js에서 관계성을 표현해줘야함

users의 id값을 posts에서는 uid로 가져가서 사용하고, comments에서는 uid로 가져가서 사용

posts의 id값을 comment에서 pid로 가져가서 사용함

홈페이지\(main.pug\)를 생성해서 로그인하는 페이지로 이동할 수 있게 해둠

로그인 페이지에서 아이디와 비밀번호를 입력받아서 맞는지 확인하고 맞으면 alert 이름님 환영합니다! -&gt; 게시판으로 / 틀리면 -&gt; alert틀림 다시 로그인화면

로그인에 대해서 검색하다가 passport에 대해서 알게 되었는데 이건 로그인을 따로 db를 설정해서 하는게아니라 소셜미디어로 로그인을 할수있게 해주는 방법인데 이건 구글에 자신의 앱을 등록해서 aws의 엔드포인트를 받는거처럼 받아서 사용하게되는 부분이다.

let models = require\(‘./models’\); 으로 model을 선언해준 다음, models.테이블이름.원하는함수 이런 방식으로 사용하면 sequelize를 사용할 수 있음

게시판부분을 구현하고자 하는데 페이징기법을 사용해보라고 했으니까 일단 검색해봤는데 로직이 따로 존재하는거같음…

공부가 필요한 부분인거같은데

페이징 처리를 하기 위해서 조건을 정해야함

1. 페이징을 하기 위해서는 한 화면에 몇개의 글을 보여줄 것인가?
2. 총 몇개의 페이지를 보여 줄 것인가?

한 번에 몇개의 글을 보여줄 것인가를 정하는 변수를 size라고 해보자

페이지수 구하기

\( 총 글 갯수 - 1 / size \) + 1

페이지 번호 구하기

페이징의 화면은 대충 

pre 1 2 3 4 5 next

이런 방식으로 뿌려지게 되는데 페이징의 시작과 끝을 구해야함

\( \( 페이지 번호 - 1 \) / 총 페이지 사이즈 \) \* 총 페이지 사이즈 + 1

7페이지를 보고 있다고 가정하면 

\( \( 7 - 1 \) / 5 \) \* 5 + 1 = 버림을 이용해서 6이 나옴

= 7 페이지를 보고 있을 때 시작페이지는 6페이지이다

pre와 next 구하기 =&gt; 만약 범위를 5개씩 잡았다면 1\*5 =5 , 6\*5=30

orm을 사용해서 db에서 데이터들을 가져오는 방식을 사용해서 했지만

그냥 쿼리를 날려서 사용하는 방법도 사용해보자

AJAX, AXIOS, fetch?

이런 것들은 원래 새로운 url을 호출할때 마다 모든 페이지를 새로 그려야하게되는데 페이지를 자주 바꾸는 것은 좋은 방법은 아니기 때문에 비동기적으로 정보를 요청하는 새로운 기술을 사용

AJAX - Asynchronous Javascript And Xml

ㄴ JavaScript를 사용한 비동기 통신, 클라이언트와 서버간에 XML 데이터를 주고 받는 기술

ㄴ XMLHttpRequest 객체를 이용하여 전체 페이지를 리로드 하지 않고 필요한 데이터를 로드할 수 있음

AXIOS

ㄴ javascript 라이브러리에서 npm i axios 로 설치 이후 사용가능함

ㄴ return값은 Promise 객체 형태

FETCH

ㄴ javascript 내장 라이브러리임 Import의 필요가 없다

ㄴ return 값은 Promise 객체 형태

사용방법 참고 : [https://ko.javascript.info/fetch](https://ko.javascript.info/fetch)

GET vs POST

get은 url 뒤에 ? 마크를 통해서 데이터를 넣어서 보내게된다. 데이터는 키-값 방식으로 저장되며, 하나 이상일때는 & 으로 구별한다.

post는 데이터 전송을 기반으로 한 요청 메소드임 -&gt; url에 붙여서 보내지 않고 데이터를 넣어서 보낸다.

현재 get방식으로 값을 넘길려고 했는데 잘 안넘어감;

post방식으로는 렌더링에서 문제가 생기는거같음

로그인 방식은 구현할때 login, login\_receive 두가지 라우터를 만들어서 post방식과 get방식을 분리한 다음

login라우터에서는 그냥 get으로 rendering만 해주었고

login\_receive에서는 post방식으로 login함수를 사용해주었음

보통 게시글을 클릭하면 새로운 페이지도 이동해서 보여주는데, 이때 이 게시글의 고유 인덱스\(보통 db 에 저장된 고유 시퀀스\)를 파라미터 등으로 보내서 그걸 한번더 axios 등으로 호출해서 관련 내용을 가져와서 화면에 그려.

전체 게시글을 그릴땐, 게시글의 시퀀스\(고유 인덱스\), 제목 작성사 이런 필요한것들에 대한 리스트만 받고, 클릭해서 들어갔을때, 컨텐츠에 대한 정보라든거 그런 디테일한 정보를 받아오고 그래

get방식으로 url에 key-value형태로 가져다 주었을때 

function getParam\(sname\) {

    var params = location.search.substr\(location.search.indexOf\("?"\) + 1\);

    var sval = "";

    params = params.split\("&"\);

    for \(var i = 0; i &lt; params.length; i++\) {

        temp = params\[i\].split\("="\);

        if \(\[temp\[0\]\] == sname\) {sval = temp\[1\];}

    }

    return sval;

}

이런 함수를 사용한다. location.search를 사용해서 url에있는걸 따올수있음. getParam\(key\)로 value값을 가져오는것이 가능하다.

하지만 이런방식은사용안했음

파일을 전송하기 위해서는 multer모듈을 사용하는 길로….

[https://m.blog.naver.com/sssang97/221642022702](https://m.blog.naver.com/sssang97/221642022702)

여기서 해결

[https://velog.io/@ejchaid/%ED%94%84%EB%A1%A0%ED%8A%B8%EC%99%80-%EB%B0%B1%EC%9D%98-%EB%8C%80%ED%99%94-Using-Fetch](https://velog.io/@ejchaid/%ED%94%84%EB%A1%A0%ED%8A%B8%EC%99%80-%EB%B0%B1%EC%9D%98-%EB%8C%80%ED%99%94-Using-Fetch)

post방식으로 전송된 데이터를 받을때 req.body에 넣어주기 위해서 

app.use\(express.urlencoded\({extended:false}\)\)

app.use\(express.json\(\)\)

이걸 사용해주는거임!

+post방식으로 데이터를 넘길때 데이터가 객체일때, 객체가 아닐때확인

-&gt;[https://velog.io/@bigbrothershin/Frontend-api-%EC%9A%94%EC%B2%AD-%EC%8B%9C-req.body](https://velog.io/@bigbrothershin/Frontend-api-%EC%9A%94%EC%B2%AD-%EC%8B%9C-req.body)

여기 참rds

JSON.stringify\(\)는 자바스크립트의 값을 JSON문자열로 변환시켜줌,

json이란 JavaScript Object Notation의 약자로, 브라우저와 서버 사이에서 오고가는 데이터의 형식임

[https://donghunee.github.io/study/2019/07/25/nodeimg/](https://donghunee.github.io/study/2019/07/25/nodeimg/)

xmlhttprequest로 비동기적으로 파일 업로드하기

[https://unikys.tistory.com/233](https://unikys.tistory.com/233)

400에러는 클라이언트에서 문제가 있다는 의미임

중복된id는 맨 첫번째의 id의 값만을 가지고올수있다.

아이디값을 변수로 주고싶어서 옛날에도 해결하지 못한 문제이다. 

for문으로 돌면서 출력을 해버렸을때는 각각을 getElementsById로 잡아서 변수를 생성하는것이 불가능하다

그래서 방법을 찾은것이 getElementByName이다. 

결과적인 방법

for문의 맨 위에 변수를 하나 두고, 변수를 만들고 싶었던 문장을 name=‘’으로 같은 이름으로 통일한다 -&gt;

for문을 돌리면서 맨마지막에 변수를 ++해서 for문을 돌때마다 숫자를 매기고 -&gt;

그 숫자에 맞춰서 getElementByName\(‘아까 해둔 이름’\) \[숫자를 매긴 변수\].innerText를 사용하게되면 for문을 돌아도 하나하나의 값을 가지고 오는 것이 가능하다

Parameter를 받을때

GET방식 -&gt; ?key=value 방식으로 넘겼을때 파라미터를 받는방식

router부분에서 받아야할때는 req.query.key 이걸 사용해서 value값을 가져오는것이 가능함

pug부분에서 받아야할때는 특정함수를 사용해서 url에서 뜯어내야한다. 

function getParam\(sname\) {

    var params = **location**.search.substr\(**location**.search.indexOf\("?"\) + 1\);

    var sval = "";

    params = params.split\("&"\);

    for \(var i = 0; i &lt; params.length; i++\) {

        var temp = params\[i\].split\("="\);

        if \(\[temp\[0\]\] == sname\) {

            sval = temp\[1\];

        }

    }

    return sval;

}

이런 함수를 사용해서 getParam\(‘key’\)로 value값을 얻어내는것이 가능하다

POST방식 -&gt; post으로 넘길때는 JSON에 항상 유의해야함 위에서도 언급했지만 서버와 클라이언트를 왔다갔다할때는 JSON방식이여야 하기 때문에 유의해야함  \*JSON의 형태는 key-value방식임

추가적으로는 req.body.key 방식으로 받은 값의 value값을 추출해 내는 것이 가능하다

세션을 통한 로그인 기능 구현시 -&gt; [https://victorydntmd.tistory.com/35](https://victorydntmd.tistory.com/35) 

여기를 참고해서 구현에 성공

페이지네이션 -&gt; 성공

JWT을 이용하게 된다면 -&gt; [https://victorydntmd.tistory.com/116](https://victorydntmd.tistory.com/116)

세션을 베이스로 했었는데 jwt를 이용하게 되면서 쿠키로 jwt를 설정하는 방식으로 구현 성공

세션을 이용해서 로그인을 유지하고, 세션의 정보로 가지고 다른 페이지에서도 사용하는데 성공

jwt방식으로 변경을 했음 -&gt; 로그인 시, 로그인한 정보가 맞다면 jwt토큰을 생성해서 그 토큰을 쿠키로 집어 넣어두었고 원래 다른 페이지들에서도 사용했던 부분은 사용할때마다 쿠키에서 토큰을 가져와서 verify를 이용해서 사용하는 것으로 변경

페이지네이션도 구현했음 게시물은 7개씩 끊어서 나올수 있도록 했고 게시물에 따라서 페이지 갯수를 구현

글 수정, 삭제 기능을 위해서 사용자가 작성했던 게시물과 댓글들을 한꺼번에 볼 수 있는 페이지를 만들었음

게시물부분에는 제목과 삭제버튼이 있음. 삭제버튼을 누르게 되면 확인을 거친 후, 해당 게시물을 삭제. 제목을 누르게 된다면 새로운 창이 뜨고 해당 부분에서 제목, 내용을 수정할 수있고 새로운 사진을 첨부하는 것이 가능함

댓글 부분에는 댓글과 삭제버튼이 있음. 삭제버튼을 누르게 되면 확인을 거친 후, 해당 댓글을 삭제. 댓글을 누르게 된다면 해당 댓글이 있는 위치로 이동하게됨

spring이란 무엇인가에 대해서 궁금하다.

dotenv를 사용해서 환경변수도 한쪽으로 빼두자

-&gt; 여기서 문제인건 .env파일로 중요한 정보들을 따로 빼두고 .gitignore로 정보들이 들어있는 파일을 git에 올리지 않고 정보유출을 막는건데 

ec2에 띄워서 사용하기 위해서는 git에 올려둔 것들을 clone해와서 사용하는 방법으로 하게되면 정보들은 같이 따라가지를 않아서 문제가 생긴다.   


