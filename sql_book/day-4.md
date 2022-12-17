# Day 4

_Hello World 실행하기_\


## Docker로 MySQL 띄우기..

\
현재 MySQL을 Docker를 통해서 띄우고 사용하고 있다. Docker로 MySQL 설치 안타깝게도 M1은 image를 pull 받을 때도 플랫폼을 명시해주고 받아야 한다는 점..!

```
docker run --platform linux/amd64 -p 3306:3306 
--name [컨테이너 이름] 
-e MYSQL_ROOT_PASSWORD=[루트 유저 비밀번호] 
-e MYSQL_DATABASE=[데이터베이스 이름] 
-e MYSQL_PASSWORD=[비밀번호]
 -d mysql
```

\
\


이렇게 실행하면 자동으로 docker에서 설치한 mysql이 돌아가게 된다. 자 돌아가는게 확인됬으면 docker에서 cli을 통해서 접속하는 것도 방법이고

```
docker exec -it mysql bash
```

를 통해서 접속하는 것도 방법임\
이제는

```java
mysql -u root -p
```

를 통해서 mysql에 접속\


```java
show databases;
```

를 통해서 존재하는 데이터베이스를 확인 -> 현재 책에서 사용할 샘플은 Sample임\


```java
use sample;
```

을 통해서 sample이란는 데이터베이스를 사용한다겠다고 선언\
\
그러면 일단 docker에 띄워둔 컨테이너에 샘플 파일을 넣어줘야한다.

\
docker ps로 확인한 container id를 뽑아서 기억해두자

```
docker cp 파일 컨테이너아이디:/
```

를 통해서 컨테이너에 파일 복사 -> 컨테이너에 접속 -> 파일적용

이제는 select문을 사용해서 조회를 해보려고 했는데 -> 한국어로 나와야 하는 곳에 ???가 찍혀서 나온다 docker mysql 한글 깨짐으로 검색해보니까... config에서 수정해야 하는 부분이 있더라\
허허 역시 vim도 없기 때문에 침착하게 vim 먼저 설치하자

```
apt-get update
apt-get install vim
```

\


자일단 설치했으면 vim etc/mysql/my.cnf로 파일에 들어가보면 2개의 파일이 있는데 첫 번째 파일로 들어가면\
거의? 반 파일이 있을텐데 거기에

```
[client]
default-character-set=utf8

[mysql]
default-character-set=utf8


[mysqld]
collation-server = utf8_unicode_ci
init-connect='SET NAMES utf8'
character-set-server = utf8
```

이렇게 입력해주고 컨테이너를 재시작해주고 mysql에 접속 후\
show variables like 'c%';\
이걸로 적용된 것을 확인해주면 한국어 패치 끝..!\
\
~~세팅 디럽게오래걸렸네...~~\
\


## Select해보자

```
select * from sample21;
```

자 SQL을 작성할 때 **스페이스바에 주의**해서 작성하자 그리고 항상 \*\*마지막에 세미콜론(;)\*\*을 넣어주자\
다음으로 봐야하는 것은 \*(와일드카드)이다\
\*은 모든 열(필드)를 의미한다.\
\
위의 명령어를 잘라서 보면 -> select, \*, from, 테이블명\
이렇게 나누어져있다\
크게 select, \*, from을 보고 **예약어**라고 부른다\
**가장 중요한건 -> 예약어, 데이터베이스 객체명은 대소문자를 구별하지 않는다는 점!**

\
\
기본적으로 select(조회)를 하게 되면 표 형식의 데이터가 출력됨\
해당 표는 행 + 열으로 구성되어있음\
행 = record\
열 = 컬럼(필드)\
\
각 데이터에는 필드에 맞는 데이터 타입이 존재한다\
숫자로 구성된 데이터 : 수치형 데이터\
임의의 문자로 구성된 데이터 : 문자열형 데이터\
날짜와 시간을 나타내는 데이터 : 날짜시간형 데이터\
아무 것도 저장되어 있지 않은 상태 : null\
\
\
\
\
\
