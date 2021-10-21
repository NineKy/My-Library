# Day 14

sudo yum install mysql

### AWS에서 데이터베이스 환경을 사용 : RDS <a href="e4fda241-422b-4457-9f99-a2745b642d3e" id="e4fda241-422b-4457-9f99-a2745b642d3e"></a>

웹 서비스의 벡엔드를 다룬다고 했을 떄 애플리케이션 코드를 작성하는 것만큼 중요한 것이 데이터베이스를 다루는 일이다.

이번에는 직접 데이터베이스를 설치하지는 않음

→만약 직접 설치해서 다루게 된다면 모니터링, 알람, 백업, HA 구성 등을 모두 직접 해야함

#### AWS에서는 위에서 언급한 작업을 모두 지원하는 관리형 서비스 : RDS(Relational Database Service)를 제공 <a href="9bc3ef60-b2be-43c7-9b80-ad37b067f531" id="9bc3ef60-b2be-43c7-9b80-ad37b067f531"></a>

#### RDS는 AWS에서 지원하는 클라우드 기반 관계형 데이터베이스 <a href="0d72f75c-e3a5-4a7e-a282-7c33cad7bfff" id="0d72f75c-e3a5-4a7e-a282-7c33cad7bfff"></a>

추가로 조정 가능한 용량을 지원하여 예상치 못한 양의 데이터가 쌓여도 비용만 추가로 내면 정상적으로 서비스가 가능

#### 1. RDS 인스턴스 생성하기 <a href="624ecb28-7a1c-4590-97fd-647016987c7a" id="624ecb28-7a1c-4590-97fd-647016987c7a"></a>

아마존에서 RDS 대시보드에서 데이터베이스 생성을 눌러보면

1.  엔진 옵션에서 MariaDB 선택

    * 굳이 다른 데이터베이스를 선택할 이유가 있는게 아니면 MySQL, MariaDB, PostgreSQL 중에 고르자 그나마 베스트는 MariaDB임 이유는
      * 가격
        * RDS의 가격은 라이센스 비용의 영향을 받는다.
        * 동일한 사양 대비 더 가격이 상용 데이터베이스(오라클, MSSQL)>오픈 소스(MySQL, MariaDB, PostgreSQL)
      * Amazon Aurora(오로라) 교체 용이성
        * Amazon Aurora는 AWS에서 MySQL과 PostgreSQL을 클라우드 기반에 맞게 재구성한 데이터베이스임
        * 성능도 RDS-MySQL보다 5배, RDS-PostagreSQL보다 3배 더 빠르고
        * AWS에서 직접 엔지니어링 하고 있기 때문에 발전 가능성도 높음
    * MariaDB를 선택하고 혹시 더 데이터베이스 사용량이 높아지면 Aurora로 이전하라

    \*MariaDB의 장점

    * MySQL을 기반으로 만들어졌기 때문에 쿼리를 비롯해서 전반적으로 비슷하기 때문에 편함
    * 동일한 하드웨어 사양으로 MySQL보다 향상된 성능
    * 좀 더 활성화된 커뮤니티
    * 다양한 기능
    * 다양한 스토리지 엔진
      * 구글에서 "MYSQL에서 MARIADB로 마이그레이션 해야 할 10가지 이유" 로 검색해서 이유 나중에 검색 해봐
2. 템플릿에서는 프리 티어 선택
3. 설정에서 사용자 이름과 마스터 암호를 설정
   * 메모에 저장해둠
4. db인스턴스 크기 : 버스터블 클래스(t 포함)
   * db.t2.micro
5. 스토리지
   * 범용(SSD)
   * 할당된 스토리지 : 20
   * 스토리지 자동 조정 : 스토리지 자동 조정 활성화
   * 최대 스토리지 임계값 : 1000
6. ...
7. 연결
   * 퍼블릭 액세스 가능 : 예
     * 여기서 예를 해두고 보안 그룹에서 지정된 IP만 접근하도록 막을 예정

첫 날에 진행하는 도중에 잘안됬음 → Intellij에서 db를 연동해서 사용하려고하니까 안됬음 ㅠ 스키마에 안나와서 뭐가 문제지하고 구글링을 통해 [https://velog.io/@johnwi/Ch07.-AWS-RDS](https://velog.io/@johnwi/Ch07.-AWS-RDS) 여기서 찾아봤음

#### Trouble Shooting(변경사항) <a href="0f7ca174-5f3e-4957-9cce-13d5a831b8bc" id="0f7ca174-5f3e-4957-9cce-13d5a831b8bc"></a>

rds를 처음에 생성시, 추가 구성을 눌러서

초기 데이터베이스 이름 : 이걸 안해줬던 거같음

* 여기보면 데이터베이스 이름을 지정하지 않으면 Amazon RDS에 데이터베이스를 생성하지 않는다

라고 적혀있는데 이번에는 다시 SpringBoot-Book이라는 이름으로 진행해보자

#### 2. RDS 운영환경에 맞는 파라미터 설정하기 <a href="71e505b8-7ca3-4d62-9725-609897cbc237" id="71e505b8-7ca3-4d62-9725-609897cbc237"></a>

설정 3가지

* 타임존
* Character Set
* Max Connection

1. 파라미터 그룹으로 들어감
2. 파라미터 그룹 생성 버튼
   * 파라미터 그룹 패밀리가 전에 RDS에서 만든 MariaDB와 같은 버전을 맞춰야함
3. 다음으로 편집버튼을 눌러서 편집 모드로 전환하고 설정값들의 변환을 시작

여기서부터 시작

* time\_zone을 검색해봐서 Asia/Seoul을 선택해서 타임존을 변경
*   Character Set

    * character를 검색해서 모든 값을 utf8mb4로 변경해주고
    * collation을 검색해서 collation\_connection, collation\_server이 2개는 utf8\_general\_ci로 변경

    utf8과 utf8mb4의 차이는 이모지 저장 가능 여부임
* Max Connection은 인스턴스 사향에 따라서 자동으로 정해지는데 좀 더 넉넉한 값인 150으로 수정

이제는 파라미터 그룹을 데이터베이스에 연결

데이터베이스로 들어가서 수정 버튼을 누름

DB 파라미터 그룹에서 아까 만들어뒀던 DB파라미터 그룹을 설정해주고 저장

그리고 재부팅을 해준다

### 3. 내 PC에서 RDS에 접속해보기 <a href="8ca81d57-7f9d-4532-bd73-e4eabfc60bb9" id="8ca81d57-7f9d-4532-bd73-e4eabfc60bb9"></a>

로컬 PC에서 RDS로 접근하기 위해서 RDS의 보안 그룹에 본인 PC의 IP를 추가해야함

1. RDS의 세부정보 페이지에서 보안 그룹에 수정이 필요한데 이번에 사용한 보안 그룹은 launch-wizard2를 사용했기 때문에 그것의 보안 그룹 아이디를 복사
2. 현재 사용중인 default에 EC2의 보안그룹(launch-wizard2)를 추가
3. 현재 내 pc의 ip를 등록

이렇게까지하면 개인 PC, EC2간의 연동 설정은 모두 끝났다 : 로컬에서 테스트해보자

#### Database 플러그인 설치 <a href="51c798af-96b2-46d7-87d2-08259070ed9a" id="51c798af-96b2-46d7-87d2-08259070ed9a"></a>

로컬에서 원격 데이터베이스로 붙을 떄 GUI 클라이언트를 많이 사용함

현재는 인텔리제이에 Database 플러그인을 설치해서 진행

일단 RDS의 정보페이지에서 엔드포인트를 확인

* 엔드포인트란 접근이 가능한 URL이기 때문에 메모장에 복사해둠

인텔리제이에서 Database Navigator를 install

***

#### Intellij Ultimate을 사용하고 있기 때문에 굳이 깔지 않고 <a href="23596db7-7221-4d4e-a78d-ba1485ae2c18" id="23596db7-7221-4d4e-a78d-ba1485ae2c18"></a>

전체 검색 → command+shift+a

를 통해서 database를 검색하면 Database플러그인과 똑같이 생긴게 뜰꺼임 거기서부터 진행

일단 +을 눌러서 MariaDB를 만들어주고

* Name 항목 에는 원하는 이름
* Host 항목에는 우리가 설정한 RDS의 엔드포인트
* User 항목에는 RDS에서 설정한 id
* Password 항목에는 RDS에서 설정한 password
* Database 항목에는 mysql

이렇게 입력 후 TestConnection을 통해서 테스트연결이 잘 되는지 확인해본다

정상적으로 진행되었다면

use 우리가만든(rds생성시 최초의 데이터베이스-빼놓고했던거) rds이름

```
use SpringBoot_Book;
```

이걸로 정상적으로 연결되어있는 것을 확인 → 사실 schemas에서 보면 이름이 있으니까 보고해도 상관없음

* 처음에 schemas에 하나밖에없을수도 있는데 마우스 우클릭을 통해 Database Tool → manage shown schemas에서 all schemas하면 다 보임

데이터베이스가 선택된 상태에서 현재의 character\_set, collation설정을 확인

```
show variables like 'c%';
```

이렇게 확인해보면 다른필드들은 모두 utf8mb4가 정상적으로 적용되어있는데 character\_set,collation2가지는 latin1이기 때문에 변경이 필요함

```
ALTER DATABASE SpringBoot_Book
CHARACTER SET = 'utf8mb4'
COLLATE = 'utf8mb4_general_ci';
```

를 통해서 변경해주고

다시

```
show variables like 'c%';
```

를 통해서 정상적으로 변경되었는지 확인

#### 타임존을 확인 <a href="23d3cdea-291d-4527-8f0c-b79b71803dfe" id="23d3cdea-291d-4527-8f0c-b79b71803dfe"></a>

select @@time\_zone, now();

→정상적으로 Asia/Seoul이 나오고

#### 한글명이 잘 들어가는지 간단한 테이블 생성을 통해서 확인 <a href="8b431832-fcbc-4fb2-8464-062469b9d3ab" id="8b431832-fcbc-4fb2-8464-062469b9d3ab"></a>

```
CREATE TABLE test(
    id bigint(20) NOT NULL AUTO_INCREMENT,
    content varchar(255) DEFAULT NULL,
    PRIMARY KEY(id)
    ) ENGINE = InnoDB;

insert into test(content) values ('테스트');
select * from test;
```

이렇게보면 한글 데이터가 잘 등록되는 것을 확인

### 4. EC2에서 RDS에서 접근 확인 <a href="f4dc4e7a-ebe2-4189-872e-ca79c69d3a09" id="f4dc4e7a-ebe2-4189-872e-ca79c69d3a09"></a>

터미널에서

```
ssh freelec-springboot2-webservice
```

로 ec2에 접속해서

```
sudo yum install mysql
```

Mysql CLI을 설치

설치가 다 되었으면

로컬에서 접근하는 것과 같이

```
mysql -u kyu9 -p -h springboot-book.cjkbpw0rm3r3.ap-northeast-2.rds.amazonaws.com
```

\-u 뒤에는 rds에서 설정한 아이디를 입력하고 맨 뒤에는 엔드포인트를 입력해줌

비밀번호는 rds에서 설정한 비밀번호를 입력하면됨

MariaDB에 접근에 성공했으면

```
show databases;
```

로 우리가 생성했던 SpringBoot\_Book 데이터베이스가 있는지 확인한다
