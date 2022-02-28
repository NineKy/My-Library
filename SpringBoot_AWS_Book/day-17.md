# Day 17

## Travis CI 배포 자동화

CI, CD 환경을 구축하면 개발자가 직접 스크립트를 실행함으로써 발생하는 불편을 개선하는 것이 가능함

* CI(Continuous Integration - 지속적 통합) : 코드 버전 관리를 하는 VCS 시스템(Git, SVN 등)에 PUSH 되면 자동으로 테스트와 빌드가 수행되어 안정적인 배포 파일을 만드는 과정
* CD(Continuous Deployment - 지속적인 배포) : CI의 빌드 결과를 자동으로 운영 서버에 무중단 배포까지 진행되는 과정

현대의 웹 서비스 개발에서는 하나의 프로젝트를 여러 개발자가 함께 개발을 진행

→ 개발자 각자가 개발한 코드를 합쳐야 할 때마다 큰일이였다.

이를 CI가 개발됨에 따라서 개발자 각자가 원격 저장소로 푸시가 될 때마다 코드를 병합하고, 테스트 코드와 빌드를 수행하면서 자동으로 코드가 통합되어 수동으로 코드를 통합할 필요가 없어짐

CD 또한 마찬가지. 1,2개의 서버에 개발자가 수동으로 배포를 할수는 있지만 서버의 수가 많아지고 급할 경우엔ㄴ 힘들기 때문에 이 또한 자동화를 시켜버림

**CI 도구를 도입했다고 해서 CI를 하고 있는것은 아니다**

CI의 4가지 규칙

* 모든 소스 코드가 살아있고(실행되고) 누구든 현재의 소스에 접근할 수 있는 단일 지점을 유지할 것
* 빌드 프로세스를 자동화해서 누구든 소스로부터 시스템을 빌드하는 단일 명령어를 사용할 수 있게 할 것
* 테스팅을 자동화해서 단일 명령어로 언제든지 시스템에 대한 건전한 테스트 수트를 실행할 수 있게 할 것
* 누구나 현재 실행 파일을 얻으면 지금까지 가장 완전한 실행 파일을 얻었다는 확신을 하게 할 것

→여기서 중요한 것은 테스팅 자동화이다. 지속적으로 통합하기 위해서는 무엇보다 이 프로젝트가 완전한 상태임을 보장하기 위해서 테스트 코드가 정확하게 구현되어있어야 한다.

### Travis CI

travis-ci.org

로 들어가서 계정명 → setting 들어가면 github아이디와 연동되어있기 때문에 내 repository들이 뜨고 거기서 내가 사용한 repository를 사용하도록 오른쪽의 상태바를 활성화

이렇게까지만하면 웹페이지에서 다뤄야할 부분은 끝났고 상세한 설정은 프로젝트의 yml파일로 진행

travis CI 설정은 프로젝트에 존재하는 .travis.yml 파일로 한다

### .yml이란?

* YAML(야믈)이라고 하며, 이 파일 확장자는 JSON에서 괄호를 제거한 것
* 기계에서 파싱하기 쉽게, 사람이 다루기 쉽게

build.gradle과 같은 위치에서 .travis.yml을 생성하고 코드를 추가

```yaml
language: java
jdk:
	- openjdk8

branches:
	only:
		- master

#Travis CI 서버의 Home
cache:
	directories:
		- '$HOME/.m2/repository'
		- '$HOME/.gradle'

script: "./gradlew clean build"

#CI 실행 완료시 메일로 알림
notifications:
	email:
		recipients:
			- "kyudo97@naver.com"
```

* branches
  * Travis CI를 어느 브랜치가 푸시될 때 수행할지 지정
  * 현재 옵션은 오직 master 브랜치에 push 될 때만 수행
* cache
  * 그레이들을 통해 의존성을 받게 된다면 이를 해당 디렉토리에 캐시하여, 같은 의존성은 다음 배포 때부터 다시 받지 않도록 설정
* script
  * master 브랜치에 푸시되었을 때 수행하는 명령어
  * 여기서는 프로젝트 내부에 둔 gradlew을 통해 clean & build을 수행
* notifications
  * Travis CI 실행 완료시 자동으로 알림이 가도록 설정

```
org.gradle.api.internal.tasks.testing.TestSuiteExecutionException: Could not complete execution for Gradle Test Executor 1.
```

라는 에러가 나서 travis에서도 에러가났다.. 이건 다시 만들던가해야할듯?

## S3란?

### AWS에서 제공하는 일종의 파일 서버

이미지 파일을 비롯한 정적 파일들을 관리하거나 지금 진행하는 것 처럼 배포 파일들을 관리하는 등의 기능을 지원. - 이미지 업로드를 구현할 때 S3을 이용해서 구현하는 경우가 많음

S3 + AWS + Travis CI 를 연동하게 된 그림

![](../.gitbook/assets/B3D5F40F-3B99-4378-BB4A-E0B48FCD1DCD\_1\_201\_a.jpeg)

Travis CI 와 S3을 연동 : 실제 배포는 AWS CodeDeploy라는 서비스를 이용하지만 S3의 연동이 먼저다 이유는 Jar 파일을 전달하기 위해서이다.

CodeDeploy는 저장기능이 없다. 그래서 Travis CI가 빌드한 결과물을 받아서 CodeDeploy가 가져갈 수 있도록 보관할 수 있는 공간이 필요한데 이것은 S3을 이용

* CodeDeploy가 빌드도 하고 배포도할수는 있음. CodeDeploy에서는 깃헙 코드를 가져오는 기능을 지원하기 때문. 하지만 이렇게 할 때 빌드 없이 배포만 필요할 때 대응하기 어려움
* 빌드와 배포가 분리되어 있으면 예전에 빌드되어 만들어진 Jar을 사용하면 되지만, CodeDeploy가 모든 것을 하게 될 땐 항상 빌드를 하게 되니 확장성이 많이 떨어짐 따라서 빌드와 배포는 분리해서 진행하는 것이 좋음

Travis CI와 AWS S3연동

1. AWS Key 발급

일반적으로 AWS 서비스에 외부 서비스가 접근할 수 없다. 따라서 접근이 가능한 권한을 가진 key를 생성해서 사용해야함. AWS에서는 이러한 인증관 관련된 기능을 제공하는 서비스로 IAM(Identity and Access Management)

IAM은 AWS에서 제공하는 서비스의 접근 방식과 권한을 관리한다. IAM을 통해서 Travis CI 가 AWS의 S3와 CodeDeploy에 접근할 수 있도록 해야함

IAM을 검색해서 들어가서 사용자를 하나 추가

이름을 설정하고, 프로그래밍 방식 액세스 칸을 체크해주고 다음

기존 정책 직접 연결을 누르고

* AWSCodeDeployFullAccess
* AmazonS3FullAccess

을 추가해주고 다음

(실제 서비스 회사에서는 권한도 S3와 CodeDeploy를 분리해서 관리하기도 합니다만 여기서는 간단하게 둘을 합쳐서 관리하겠습니다. 2개의 권한이 설정되었으면 다음으로 넘어감)

키-값형태를 추가해줘야하기 때문

키 : name

값 : freelec-travis-deploy

메모 앱에 액세스키랑 비밀액세스키를 기록해둠

이제는 Travis CI에 엑세스키와 비밀엑세스키를 여기에 등록하고 등록된 값들을 .travis.yml에 $AWS\_ACCESS\_KEY, $AWS\_SECRET\_KEY라는 이름으로 사용가능하다

## S3 버킷 생성

AWS의 S3 서비스는 일종의 파일 서버이다. 순수하게 파일들을 저장하고 접근 권한을 관리, 검색 등을 지원하는 파일 서버의 역할을 한다.

S3는 보통 게시글을 쓸 때 나오는 첨부파일 등록을 구현할 때 많이 이용함. 파일 서버의 역할을 하기 때문인데, Travis CI에서 생성된 Build 파일을 저장하도록 구성할 것이다. S3에서 저장된 Build파일은 이후 AWS의 CodeDeploy에서 배포할 파일로 가져가도록 구성할 예정

이제는 버킷을 생성하는데 이 버킷에 배포할 Zip 파일이 모여있는 장소임을 의미하도록 짓는 것을 추천

freelec-springboot-build

라는 이름, 모든 퍼블릭 액세스 차단이 기본으로 체크되어있는데 잘 체크되어있는지 확인

* 현재 프로젝트는 이미 깃헙에 오픈소스로 풀려있으니 문제하없지만, 실제 서비스에서 할 때는 Jar 파일이 퍼블릭일 경우, 누구나 내려받을 수 있어 코드나 설정값, 주요 키 값들이 다 탈취 당할 수 있음
* 퍼블릭이 아니더라도 우리는 IAM 사용자로 발급받은 키를 사용할것이니까 접근이 가능하다. 그래서 모든 액세스를 차단하는 설정에 체크

.travis.yml

```yaml
before_deploy:
  - zip -r freelec-springboot2-webservice *
  - mkdir -p deploy
  - mv freelec-springboot2-webservice.zip deploy/freelec-springboot2-webservice.zip

deploy:
  - provider: s3
    access_key_id: $AWS_SECRET_KEY # Travis repo settings에 설정된 값
    secret_access_key: $AWS_SECRET_KEY # Travis repo settings에 설정된 값
    bucket: freelec-springboot-build # S3 버킷
    region: ap-northeast-2
    skip_cleanup: true
    acl: private #zip 파일 접근을 private 으로
    local_dir: deploy #before_deploy에서 생성한 디렉토리 
    wait-until-deployed: true
```

* before\_deploy
  * deploy 명령어가 실행되기 전에 수행
  * CodeDeploy는 Jar 파일을 인식하지 못하므로 Jar+기타 설정 파일들을 모아 압축
* zip -r freelec-springboot2-webservice
  * 현재 위치의 모든 파일을 freelec-springboot2-webservice 이름으로 압축
  * 명령어의 마지막 위치는 본인의 프로젝트 이름이어야 함
* mkdir -p deploy
  * deploy라는 디렉토리를 Travis CI가 실행중인 위치가 생성
* mv [freelec-springboot2-webservice.zip](http://freelec-springboot2-webservice.zip) delpoy/freelec-springboot2-webservice.zip
  * [freelec-springboot2-webservice.zip](http://freelec-springboot2-webservice.zip) 파일을 deploy/freelec-springboot2-webservice.zip으로 이동
* deploy
  * S3로 파일 업로드 혹은 CodeDeploy로 배포 등 외부 서비스와 연동될 행위들을 선언
* local\_dir: deploy
  * 앞에서 생성한 deploy 디렉토리를 지정
  * 해당 위치의 파일들만 S3로 전송

이렇게 .travis.yml을 수정해주고 나서 깃헙에 자동으로 푸쉬하면 자동으로 EC2에도 들어가게 되고 자동으로 버킷에 업로드가 되어있다.
