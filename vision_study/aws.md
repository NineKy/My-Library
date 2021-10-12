---
description: 유료 강의를 들으면서 공부
---

# AWS 강의

강의끝나면 할일: aha로 만들어본 서버를 aws로 실제로 띄워보는 역활

aha는 결국 localhost로 해서 활용한 서버이기 떄문에 실제로 해보는 거까지가 할일

AWS Management Console

[https://ap-northeast-2.console.aws.amazon.com/console/home?region=ap-northeast-2\#](https://ap-northeast-2.console.aws.amazon.com/console/home?region=ap-northeast-2#)

강의

내가 산거

[udemy.com/course/aws-beginner-sk/learn/lecture/20062194\#content](http://udemy.com/course/aws-beginner-sk/learn/lecture/20062194#content)

생활코딩

 [https://www.youtube.com/watch?v=lmDslNR\_ymc&list=PLuHgQVnccGMC5AYnBg8ffg5utOLwEj4fZ&index=13](https://www.youtube.com/watch?v=lmDslNR_ymc&list=PLuHgQVnccGMC5AYnBg8ffg5utOLwEj4fZ&index=13)

필요한것만 들으면 됨

nodejs aws배포 검색해서 나온 게시물

[https://velog.io/@jdm1219/Express-EC2%EC%97%90-%EB%B0%B0%ED%8F%AC%ED%95%B4%EB%B3%B4%EA%B8%B02-cxjxkae7hd](https://velog.io/@jdm1219/Express-EC2%EC%97%90-%EB%B0%B0%ED%8F%AC%ED%95%B4%EB%B3%B4%EA%B8%B02-cxjxkae7hd)



### 1장

Amazon Web Service

클라우드 컴퓨팅 - 원격상의 존재하는 서버

서버리스 기능 지원 - 서버리스란 클라우드가 서버를 작동시키고, 관리하며 메모리할당에 유연한 방식을 채택하여 지원자 스스로 생존한다.

어떻게 설명할 수 있는지 -&gt; 클라우드 안에 저장장치나, pc방같은 사용시에 돈이 나가거나 모뎀에 랜선같은게 모두 통합되어있는 방식이라고 생각하면 될듯

aws자격증 -&gt; developer associate

ㄴ 클라우드쪽에서 aws자격증이 도움이 많이됨

1-2장-계정생성

as you pay go 서비스 : 서비스를 사용하는 만큼 비용을 지불하는 개념 / 데모버전으로 무료버전들도 있음

root 사용자 : 처음 계정을 만들게되면 root계정이 되고 그 안에서 새로운 유저들을 생성, 삭제, 권한부여가 가능하다

email: [kyudo97@naver.com](mailto:kyudo97@naver.com)

뎁스 -&gt; 



### 2장 - IAM

Identity and Access Management : 사용자들의 계정에 대한 관리가 주 목적인 서비스임

ㄴ 유저를 관리하고 접근 레벨 및 권한에 대한 관리

-유저 A를 만들었고, IAM은 A에 대한 접근키와 비밀키를 생성해줌 A는 이걸가지고 서비스를 사용할 수 있음

-세밀한 접근 권한 부여 기능\(Granular Permission\) =&gt; 테이블에 대한 생성, 삭제, 복사기능 원하는 권한을 A에게 주는 것이 가능

-비밀번호를 수시로 변경 가능하게 해줌 =&gt; 실습을 통해…. 

-Multi-Factor Authentication\(다중 인증\)기능 =&gt; 적극 권장하는 부분임

루트유저가 생성 가능한 것들

그룹\(Group\) : 하나의 그룹은 하나 혹은 다수의 유저가 존재할 수 있음 

유저\(User\) : 사용자들 

역활\(Role\) : 유저와 의미는 비슷하지만, 하나 혹은 다수의 정책을 지정하는 것이 가능함-&gt;유저마다 다양한 정책을 부여함으로써 다른 권한을 위임할 수 있음

정책\(Policy\) : JSON의 형태로 되어있는 문서, 세밀한 접근 권한을 일일이 설정해서 하나의 정책doc을 만듬 =&gt; 다양한 정책을 만들어 다양한 접근 레벨 및 권한이 가능, 정책은 그룹과 역활에 추가할 수 있음= 그 그룹안에 있는 모든 유저에게 영향이 간다

IAM은 유니버셜\(Universal\)함!\(지역설정이 필요없음\) —— 대부분의 서비스는 지역성\(regional\)임 

2-2장 - IAM 정책 시뮬레이터

ㄴ 개발환경\(Staging or Develop\)에서 실제환경\(Production\)으로 빌드하기 전, IAM 정책이 잘 작동되는지 테스트하기 위함

ㄴ IAM과 관련된 문제들을 디버깅하기에 최적화된 툴임\(이미 실제로 유저에 부여된 다양한 정책들도 테스트 가능\)

IAM Policy Simulator -&gt; 한글버전이 안됨…

2-3장 - IAM 실습

유저생성

사용자를 생성하는데 액새스키와 비밀액세스키가있는데 비밀 액세스키는 처음만들었을때말고는 확인할수있는 방법이 없으니까 .csv다운로드를 통해서 저장해두는 것도 방법임, 비밀액세스키를 한번 잃어버리면 다시만드는게 유일한 방법이다

그룹생성

처음에 선택하는 정책이 적용됨

역활생성

역활을만들면, 그 역활안에서 우리가 어떠한 서비스ㅡㄹ 차단하고 어떠한 서비스를 허용하는지에 대한 권한을 주는 역활

role은 유저와 밀접한 관계가 있다.

정책

생성하는데, 두가지 방법이있음, 서비스에 처크하는 방식과 JSON에서 수동으로 설정하는 방식



#### 3장 - EC2

Elastic Compute Cloud : 클라우드라는 공간에서, 크기가 유연하게 변경되는 기능을 제공

ㄴ 인스턴스를 키고 끌수 있으며, 켰을떄만 돈을 냄 -&gt; 시간단위로\(On-demand\), 한정된 EC2 용량\(Reserved\), 입찰 가격 적용=인스턴스의 시작과 끝기간이 중요하지 않을때\(Spot\)

ㄴㅡ on-demand: 최소한의 비용을 지불해서 쓸 떄, 단기간에 끝낼 수 있을 때\(개발 초기단계에서 test할때가 적합\), 개발시작과 끝이 미정일때

ㄴㅡreserved: 안정된,  개발시작과 끝이 정해져있을때, 요구사항이 반복되지않고 안정됬을때, 개발시간의 예측이 가능할때 할인되니까

ㄴㅡspot:  경매라고 생각

inbound: 외부에서 EC2 인스턴스로 들어오는 트래픽이고, 대표적으로 HTTP, HTTPS, SSH, RDP 등이 있음

outbound: EC2 인스턴스에서 외부로 나가는 트래픽

EC2를 사용하기위해는 EBS라는 디스크 볼륨을 요구함 -&gt; EBS란 EC2에 붙어있는 하드디스크라고 생각하면됨

3-1장 - EBS의 개념

Elastic Block Storage : 스토리지 볼륨을 생성하여, EC2 인스턴스에 부착되어 사용됨 = 컴퓨터사면 하드가 딸려서 오는 개념과 같음

ㄴ 디스크 볼륨 위에 File system이 생성

ㄴ 특정 Availability Zone에 생성 

 =&gt; aka az 하나의 region 안에 여러개의 az가 존재, 중심부로부터 그 복사본들이 az로 뿌려지고, 하나의 서버가 망가지면 az라는 백업을 통해 서비스를 해주는 Disaster Recovery가 가능하게 해줌

볼륨타입

ㄴSSD군 =&gt; 

1. General Purpose SSD\(GP2\) : 최대 10K IOPS를 지원하고 1GB당 3IOPS 속도가 나옴, 가장 많이 사용함 

2. Provisioned IOPS SSD\(IO1\) : 극도의 I/O률을 요구하는 환경에서 주로 사용됨, 10K 이상의 IOPS를 지원함, 방대한 양의 DB를 사용시 좋음, 엄청비쌈

ㄴHDD군 =&gt; 

3. Throughput Optimized HDD \(SY1\) : 빅데이터 Datawarehouse, Log 프로세싱시 주로 사용됨\(boot volume으로 사용 불가능\) 운영체제를 가질 수 없음

1. CDD HDD\(SC1\) : 파일 서버와 같이 입출력이 드문거에 사용하기 좋음,  volume 접근시 주로 사용, 역시 boot volume으로 사용 불가능하나 비용은 매우 저렴함
2. Magnetic\(Standard\) : 디스크 1GB 당 가장 싼 비용을 자랑함, boot volume으로 유일하게 사용 가능함

어떤 상황에 어떤 볼륨을 사용할지 구분 가능

3-2장 - ELB의 개념

Elastic Load Balancers : 균형

ㄴ수많은 서버의 흐름을 균형있게 흘려보내는데 중추적인 역활을 한다

ㄴ하나의 서버로 traffic이 몰리는 병목현상을 방지해줌

ㄴtraffic의 흐름을 unhealthy instance -&gt; healthy instance로 해줌

특정 상황을 주고 어떤 ELB를 사용해야하는 가

종류

1. Application Load Balancers : OSI Layer7에서 작동됨\(가장 밖에서 사용됨\)

ㄴ http, https와 같은 traffic의 load balancing에 가장 적합

ㄴ고급 request 라우팅 설정을 통하여 특정 서버로 request를 보낼 수 있음 = customize가능하다

1. Network Load Balancers : OSI Layer 4에서 작동됨 \(transport layer라고 불림\)

ㄴTCP를 처리하는데 가장 적합함 = 매우 빠른 속도가 요구됨

ㄴ초당 수백만개의 request를 미세한 delay로 처리가 가능함

ㄴ구글이나 네이버같은 큰 서버에 적합

1. Classic Load Balancers : 현재 Legacy로 간주됨, 따라서 거의 안쓰임

ㄴLayer7과 Layer4의 라우팅기능을 지원함

에러메세지를 발견해서 우리에게 알려주는데 그것이 504에러이다.

웹서버나 데이터베이스 layer에서 문제해결이 가능함

X-Forwarded-For 헤더

public IP address가 DNS를 통해서 ELB로 가게되면 public ip가 private ip로 인식이 되고, EC2에서 request정보를 확인할때에는 private ip밖에 볼 수 없음 \(= 출처를 확인하지 못한다\). 따라서 이럴때는 X-Forwarded-For 헤더를 사용해서 Public ip를 확인하는 것이 가능하게 됨

3-3장 - Route53

aws에서 제공하는 dns 서비스 : EC2 instance, S3 Bucket, Load Balancer

도메인주소를 구매해서 위서비스로 백엔드에 연결해주는 방식

구매..해야하나?

3-4 EC2 실습

EC2는 region성임

인스턴스 구성중, 네트워크부분에서 default로 vpc가 되어있음

 vpc는 가상의 클라우드 공간을 의미함 = 하나의 vpc안에 다양한 인스턴스들이 공유되는 방식임

보안-어떤 트래픽을 받을지 

위치무관: 어디에서든 접속이 가능하기떄문에 production에서는 권장하지 않음-실습에서는 ㄱㅊ

instance -&gt; 클라우드의 가상 서버

ssh -&gt; Secure Shell, 네트워크 상의 다른 컴퓨터에 로그인하거나 원격 시스템에서 명령을 실행하고 다른 시스템으로 파일을 복사할 수 있도록 해주는 응용프로그램 또는 그 프로토콜

tcp -&gt; Transmission Control Protocol, 전송 제어 프로토콜, IP의 핵심 프로토콜 중 하나임

IP -&gt; Internet Protocol

인스턴스 실행-&gt;터미널을 실행 후,  키페어를 다운로드받은 위치로 위치변경

cd downloads

허가변경 : chmod 400 awsleaner.pem

두번째 명령아 : ssh -i "awsleaner.pem" ec2-user@ec2-54-180-100-100.ap-northeast-2.compute.amazonaws.com

최초 연결시 물어봄 yes입력시 인스턴스로 들어오게됨

root유저가 되기 위해서 

sudo su

입력

yum update -y

운영체제를 업데이트 시키며, 관련있는 모든 패키지를 알아서 설치해주는 명령어

아파치를 설치해서 사용할꺼임

yum install httpd -y

이걸로 아파치 설치까지 완료함

service httpd start

아파치를 실행

인스턴스를 재부팅 시키면 자동으로 아파치까지 실행시켜주기 위해

chkconfig httpd on

cd /var/www/html

로 이동\(이떄는 빈폴더\)

vi index.html

로 html파일을 생성

인스턴스설정 페이지로 돌아가서, IPv4 퍼블릭 IP로 가보면 index.html에 작성했던 내용들이 정상적으로 표시되게됨



#### 4장  - RDS\(Relational DB Service\) : 관계형 데이터 베이스

ㄴ 엑셀처럼 행과 열이있는 특징이있는 다른 값들이 들어가게됨

데이터는 record라고 생각

필드는 세로

RDS의 종류로 여러가지 있지만 Aurora는 aws에서 사용하는 건데 유료임…

Data Warehousing : business intelligence, 엄청난 양의 데이터를 로드시 사용, 서로 다양한 소스로부터 데이터들이 합쳐지며 회사에 도움이 될만한 데이터들을 저장하고 있따가 필요할떄 불러옴, 

OLTP VS OLAP

OLTP: insert와 같이 종종 사용되어지는, 혹은 규모가 작은 데이터를 불러올때 사용되는 sql쿼리가 필요할 때 유용

OLAP: 매우 큰 데이터를 불러올때 사용, 주로 덩치가 큰 select 쿼리가 사용됨

4-1장 - RDS에 존재하는 Database Backups

Automated Backups\(자동 백업\)

ㄴRetention Period\(현재 날짜의 기준으로 1-35일\) 안의 어떤 시간으로 돌아가게 할 수 있음

ㄴ어떤 날짜의 백업을 확인할때, 그날 생성됨 스냅샷과 Transaction logs을 참고하기 떄문에 항상 이 두개를 주시하고 있는다

ㄴ이는 디폴트로 저장되게 되어있으며, 백업 정보는 S3 버킷에 저장되어있음\(RDS의 크기보다 백업크기가 크게되면 유료가 됨\)

ㄴI/O suspension이 존재하게됨\(딜레이가 있다\)

DB Snapshots\(데이터베이스 스냅샷\) -&gt; 이미지 캡쳐와 비슷한 의미

ㄴ 주로 사용자에 의해서 실행된다.

ㄴ 원본 RDS가 삭제되어도 스냅샷이 존재한다 =&gt; 스냅샷만으로도 DB를 복원이 가능하게됨 ab는 원본RDS가 사라지면 ab도 사라지게 됨

RDS 백업시 일어나는 일

원본으로 새로운 DB를 복원시, RDS Instance와 RDS Endpoint가 생성되고 이 둘은 완전히 다른 객체임

원본은 original.~~이고 백업은 restored.~~이름이 붙게 됨

4-2장 - Multi AZ, Read Replicas \(중요한 특징임\)

Multi AZ\(Multi Availability Zone\)

ㄴ원래 존재하는 RDS DB에 무언가 변화가 생길때, 다른 AZ에 똑같은 복제본이 만들어짐 = Synchronize

ㄴAWS에 의해서 자동으로 관리가 이루어짐

ㄴ원본 RDS DB에 문제가 생길 시, 자동으로 다른 AZ의 복제본이 그대로 가지고 와서 사용함\(=Disaster Recovery\)

ㄴ성능개선을 위한것은 아님 성능개선을 위하면 Read Replicas를 사용

abc-2A 에서 abc-2B가 생성됨

Read Replica

ㄴProduction DB의 읽기 전용 복제본이 생성됨

ㄴ주로 Read-Heavy DB작업시, 효율성의 극대화를 위해 사용됨\(Scaling\) = 주목적임

ㄴDistaster Recovery용도가 아님

ㄴ최대 5개의 ReadReplica DB를 허용

ㄴRead Replica의 Read Replica를 생성 가능 =&gt; 생성시, 사용시 딜레이가 생기게됨

ㄴ각각의 Read Replica는 자기만의 고유 Endpoint가 존재\(RDS는 IP주소가 아닌 Endpoint로 구별\)

4-3장 - ElasticCache 로 효율적인 앱 서비스 사용가능

ㄴ클라우드 내에서 In-memory 캐시를 만들어줌

ㄴ데이터베이스에서 데이터를 읽어오는 것이 아니라 캐시에서 빠른 속도로 데이터를 읽어옴

ㄴRead-Heavy할때 빠른 것을 느낄 수 있음

2가지의 타입

-Memcached

Object 캐시 시스템, ElasticCache에서 이 프로토콜을 사용함, EC2 Auto Scaling처럼 크기가 알아서 커졌다 작아졌다 가능함, 오픈소스

이걸 사용하는경우

ㄴ 가장 단순한 캐싱 모델이 필요할때

ㄴObject caching이 주된 목적일때

ㄴ캐시 크기를 마음대로 scaling하기를 원할때

-Redis

Key-Value, Set, List와 같은 형태의 데이터를 In-Memory에 저장 가능, 오픈소스, Multi-AZ지원\(재해 복구 기능\)

이걸 사용하는 경우

ㄴList, Set과 같은 데이터 타입을 사용할때

ㄴ 리더보드처럼 데이터셋의 랭킹을 정렬하는 용도가 필요할 때

ㄴ Multi AZ기능이 사용되어져야 할 때

4-4실습

DB인스턴스 크기에서 

버스터블 클래스란: t2가 그중 하나이고 상황에 따라서 cpu 기능을 버스트 시킬수 있다는 의미임

스토리지 자동조정:scaling

ip주소가아니라 dns형태로 표현이 되어있음



bootstrap 파일

\#! = Shebang

bin/bash라는 경로를 지정해주고 bashscript에서 의무적으로 넣어줘야하는 부분임

\#!/bin/bash

yum install httpd php php-mysql -y

=아파치, php, mysql를 설치한다는 의미이고 모든 것에 대해서 강제적으로 yes라고 답한다는 의미

yum update -y

=운영체제에 업데이트를 해준다는 의미

chkconfig httpd on

=아파치 시스템을 킨다는 것, 이 커맨들르 돌려야 EC2인스턴스가 재부팅되도 아파치가 계속 켜져있다

service httpd start

=아파치 서버를 실행시켜주는 것

echo "&lt;?php phpinfo\(\);?&gt;" &gt; /var/www/html/index.php

php파일을 생성하고 index.php로 저장하고

cd /var/www/html

그 폴더로 들어가서

wget https://aws-learner-storage.s3.ap-northeast-2.amazonaws.com/connect.php

wget을 사용해서 주소에있는 파일을 다운롣 받는다는 의미임

connect.php에는 

데이터베이스 이름과 비밀번호, 호스트이름, db이름을 설정하는항목이 있꼬

RDBS로 들어가는 php코드가 있음

성공시-&gt; MySQL 접속 성공이라는 단어와 

실패시-&gt;MySQL DB 연결 실패…



인스턴스를 만들고 인스턴스보기에 들어가면 만든 인스턴스가 있을 꺼임

만든 인스턴스를 누르고, 

퍼블링 DNS를 복사하고 새로운 창에서 붙혀넣기를 통해서 만든 페이지를 확인할 수 있고

설정을 조정하기 위해서는 터미널에서 downloads로 이동한 후에 인스턴스 연결을 누르면 ssh로 시작하는 명령어를 줌 그렇게 조정하는 것이 가능하게됨

sudo su로 root권한을 획득

코드를 생각해보면 /var/www/html이라는 위치에 connect.php와 index.php를 생성했기에 /var/www/html위치로 가서 파일목록을 보게되면 두개의 파일이 있는 것을 확인할 수 있음

nano connect.php

에서 hostname을 우리가 만들었떤 RDB의 엔드포인트로 변경을 해줌

\*nano 사용법

**실행**

nano

**파일** 열기

nano -w 파일명

**저장**

Ctrl+O

**끝내기**

Ctrl+X, 정말로 끝낼 것인지를 묻는 화면에서 Y를 누르고 저장하려는 파일명을 확인한 후에 엔터

수정을 끝낸 후 /connect.php 를 붙혀서 들어가보면 에러가 뜨게 됨

=&gt;현재 RDS의 보안그룹은 RDS안에만있고, EC2의 보안그룹이 다르기 때문에 소통이 불가능하다 =&gt; RDS에서 변경이 필요함

4-4장

RDS를 생성하게 되면 자동으로 스냅샷 하나를 만들어냄

ㄴ시간에 따라서 업데이트가 되고 만들어짐

우리가 스냅샷을 찍고 싶다면, 스냅샷생성으로 찍으면됨



### 5장 - S3

Simple Storage Service = S3

가장 처음으로 런칭한 프로그램이다. 

ㄴ 안전하고 가변적인 Object 저장공간을 제공함 -&gt; 안전장치를 걸어놔서 외부에 접근이 불가능함, 이미지나 파일같은 것들은 가능하지만 운영체제를 올려서 사용하는 것은 불가능하다.

ㄴ 편리한 UI 인터페이스를 통해 어디서나 쉽게 데이터를 저장하고 불러올 수 있음

ㄴ 파일 크기는 0KB부터 5TB까지 폭넓게 지원함

ㄴ 저장 공간을 무제한

ㄴ Bucket이라는 이름을 사용함 -&gt; 디렉토리와 형태가 유사하다

ㄴ Bucket 이름은 보편적인 namespace를 사용함 -&gt; 버켓이름은 고유해야 한다.\(global하기 떄문\)

S3 Object의 구성요소

ㄴkey = 파일명

ㄴvalue = 파일에 대한 데이터

ㄴversion ID = S3의 고유 특징, 같은 파일일때 버전별로 나눌때 

ㄴ metadata = 데이터의 데이터, 파일 업로드 시간, 파일의 주인등등 파일의 정보들\(직접 수정도 가능\)

ㄴCORS\(Cross Origin Resource Sharing\) = 한 버켓의 파일을 다른 버켓에서 접근할 수 있게 해주는

S3 Data Consistency Model

1. Read after Write Consistency\(PUT\) -&gt; 업로드를 하면 즉시 사용할 수 있다. = 딜레이가 없다
2. Eventual Consistencty\(UPDATE, DELETE\) -&gt; 버켓에있는 것들 업데이트, 삭제를하면 육안으로는 바로 바뀐게 안보이는데 내부에서는 변경이 된 것

S3의 스토리지 타입

ㄴ 일반 S3 -&gt; 가장 보편적으로 사용되는 스토리지 타입, 높은 내구성\(데이터의 손실없이 유지\)과 가용성\(데이터의 접근\)을 자랑함

ㄴ S3 - IA\(Infrequent Access\) -&gt; 자주 접근하지 않으나 접근할 때 빠른 접근이 요구되는 파일이 많을 떄, 비용이 저렴하지만 데이터에 접근할떄 추가 비용이 발생함, 멀티 AZ를 통해 데이터를 저장=가용성이 높음

ㄴ S3 - One Zone IA -&gt; 단일 AZ를 통해 데이터를 저장하고, 데이터의 접근에 제한이 생김\(가용성이 조금 낮다\), 하지만 데이터 접근시 IA보다는 20%정도 저렴함

ㄴ Glacier -&gt; 거의 접근하지 않을 데이터 저장할떄 유용함, 그래서 비용이 매우 저렴하고, 데이터 접근할때 4-5시간정도 소요됨

ㄴ Intelligent Tiering -&gt; 데이터 접근 주기가 불규칙할때 매우 유용함, 데이터의 접근 주기를 자기가 알아서 분석한 후, Frequent Tier나 Infrequent Tier로 나눔, Frequent Tier가 비용이 비쌈, 최고의 비용 절감 효율을 누릴 수 있음 

요금의 기준

ㄴ GB당 

ㄴ PUT, GET, COPY 요청 횟수당

ㄴ 데이터 다운로드시, 다른 리소스로 전송시

ㄴ Metadata \(object tag\)

5-2 버켓 생성시 알아야 할 것들

S3 사용 용례

ㄴ파일 저장소\(로그, 다양한 파일들\)

ㄴ웹사이트 호스팅 -&gt; S3에다가 필요한 파일들을 올리고 S3를 실제 도메인 DNS로 사용하는 것이 가능

-&gt;Route 53를 사용해서

ㄴCORS -&gt; 버켓간은 region이 다르기 때문에 데이터의 접근이 불가능하지만 이걸 가능하게 해주는게 cors

최초로 S3버켓 생성시, private임

ㄴ다른 사람이 접근시, access denied

ㄴ해결방법: 버켓 정책 번경\(public으로 바꾸면 내부의 모든게 다 적용\), 접근 제어 리스트 변경\(파일 하나하나에 접근 권한이 존재하고 권한을 가진 사람들이 해당 파일에 접근하는 것이 가능함\)

5-3 암호화

경우 2가지

ㄴ 파일 업로드/다운로드시 -&gt; SSL/TLS가 일어남 

SSL은 웹사이트 맨 앞에 https가 붙음으로써 안전하다는 것을 알려줌

TLS은 SSL에서 파생되었지만 더 좋은 보안

ㄴ 가만히 있을 시 -&gt; SEE-S3, SSE-KMS, SSE-C

SEE-S3는 마스터키를 받는데 계속 변경됨

SSE-KMS는 aws에서 일괄적으로 관리해줌, 누가 언제 어떻게 암호를 풀었는지 기록해줌

SSE-C는 암호키를 우리가 직접 다룰 수 있게 해줌

S3 암호화 과정

-PUT요청이 생성됨

* x-amz-server-side-encryption-parameter이게 들어있으면 암호화를 해달라는 걸로 받아 들이고 암호화해줌\(헤더에 들어있음\)
* 암호화되어있지 않는 것은 어떻게 해야하나 -&gt; 버켓 정책 설정으로 가능

실습: S3는 global이기 때문에 지역이 상관없음

 태그를 이용해서 필터링

버킷 정책 생성

ㄴ 편집기가 나오게되는데 밑에 정책 생성기로 들어간다 -&gt; 영어로 막 되어있지만, 타입을 S3로 변경해주고, Principal은 IAM에서의 사용자 ARN이 들어가야 하는 부분이다 -&gt; Actions에서는 bucketpolicy 부분이 우리가 해당하는 부분이니 bucketpublicy를 선택해주고 ARN부분은 정책 생성기 단계에서 arn:aws로 시작하는 부분이 친정하게 나와있음 이걸 복사해주면 됨

=&gt; 이렇게 하고 add statement를 해주게되면 json형태로 코드가 나오고 이걸 긁어서 정책생성기에 붙혀넣기 해주면 정책이 적용이 된다.

