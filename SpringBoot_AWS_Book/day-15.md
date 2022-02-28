# Day 15

### EC2에서 프로젝트 Clone 받기 <a href="2ec4450f-6807-4eb7-86b7-20e3a310f131" id="2ec4450f-6807-4eb7-86b7-20e3a310f131"></a>

깃허브에서 코드를 받아올 수 있도록 EC2에 깃을 설치

```
sudo yum install git
```

설치하면 git —version으로 설치 상태를 확인 한 뒤에

git clone으로 프로젝트를 저장할 디렉토리를 생성하자

```
mkdir ~/app && mkdir ~/app/step1
```

생성 후

```
cd app/step1
```

이동 후 프로젝트를 다운받자

```
git clone 깃주소
```

다운 받고나서

```
./gradlew test
```

로 테스트가 잘 되는지 확인을 해봐야하는데.... 이게 안되넹

이게뭐 Permission denied가 안되면

```
chmod +x ./gradlew
```

뭐 이렇게 해서 해보라는데 일단 테스트가 안댐...

안되는대로 일단은 진행해보자

### 배포 스트립트 만들기 <a href="230b614e-a189-44af-a521-cde228c17163" id="230b614e-a189-44af-a521-cde228c17163"></a>

배포 : 작성한 코드를 실제 서버에 반영하는 것

* git clone 혹은 git pull을 통해 새 버전의 프로젝트를 받음
* gradle이나 maven을 통해 프로젝트 테스트와 빌드
* EC2 서버에서 해당 프로젝트 실행 및 재 실행

이 3가지의 과정을 모두 포괄하는 의미

이러한 과정 들을 배포할 때마다 개발자가 하나하나 명령어로 실행하는 것은

비효율적

그래서 이것을 쉘 스크립트로 작성해서 스크립트만 실행하는 과정으로 진행할 것

쉡 스크립트란 .sh 파일 확장자를 가지는 파일

vim을 사용

```
vim ~/app/step1/deploy.sh
```

이걸로 파일을 생성

vim의 간단한 튜토리얼 - http://bit.ly/2Q3BpvZ

```
#!/bin/bash
REPOSITORY=/home/ec2-user/app/step1
PROJECT_NAME=freelec-springboot2-webservice

cd $REPOSITORY/$PROJECT_NAME/

echo "> Git Pull"

git pull

echo "> 프로젝트 Build 시작"

./gradlew build

echo "> step1 디렉토리로 이동"

cd $REPOSITORY

echo "> Build 파일 복사"

cp $REPOSITROY/$PROJECT_NAME/build/libs/*.jar $REPOSITORY/

echo "> 현재 구동중인 애플리케이션 pid 확인"

CURRENT_PID=${pgrep -f ${PROJECT_NAME}.*.jar}

echo "현재 구동 중인 애플리케이션 pid: $CURRENT_PID"

if[-z "$CURRENT_PID"]; then
	echo ">현재 구동 중인 애플리케이션이 없으므로 종료하지 않습니다."
else
	echo "> kill -15 $CURRENT_PID"
	kill -15 $CURRENT_PID
	sleep 5
fi

echo "> 새 애플리케이션 배포"

JAR_NAME=${ls -tr $REPOSITORY/ | grep jar | tail -n 1}

echo "> JAR Name: $JAR_NAME"

nohup java -jar $REPOSITORY/$JAR_NAME 2>&1 &
```

* REPOSITORY=/home/ec2-user/app/step1
  * 프로젝트 디렉토리 주소는 스크립트 내에서 자주 사용하는 값이기 때문에 이를 변수로 지정
  * 마찬가지로 PROJECT\_NAME=freelec-springboot2-webservice도 동일하게 변수로 저장
  * 쉘에서는 타입 없이 선언하여 저장
  * 쉘에서는 $ 변수명으로 변수를 사용할 수 있음
* cd $REPOSITORY/$PROJECT\_NAME/
  * 제일 처음 git clone 받았던 디렉토리로 이동
  * 바로 위의 쉘 변수 설명을 따라서 /home/ec2-user/app/step1/jojo-spring-boot 주소로 이동
* git pull
  * 디렉토리로 이동한 후, master 브랜치의 최신 내용을 받음
* ./gradlew build
  * 프로젝트 내부의 gradlew로 build를 수행
* cp ./build/libs/\*.jar $REPOSITORY
  * build의 결과물인 jar파일을 복사해 jar 파일을 모아둔 위치로 복사
* CURRENT\_PID=$(pgrep -f springboot-webservice)
  * 기존에 수행 중이던 스프링 부트 애플리케이션을 종료
  * pgrep은 process id만 추출하는 명령어
  * \-f 옵셥은 프로세스 이름으로 찾는다
* if \~else \~fi
  * 현재 구동중인 프로세스가 있는지 없는지 판단해서 기능을 수행
  * process id 값을 보고 프로세스가 있으면 해당 프로세스가 종료
* JAR\_NAME=$(ls -tr $REPOSITORY/ | grep jar | tail -n 1)
  * 새로 실행할 jar 파일명을 찾는다
  * 여러 jar 파일이 생기기 떄문에 tail -n로 가장 나중의 jar파일을 변수에 저장
* nohup java -jar $REPOSITORY/$JAR\_NAME 2>&1 &
  * 찾은 jar 파일명으로 해당 jar 파일을 nohup으로 실행
  * 스프링 부트의 장점으로 특별히 외장 톰캣을 설치할 필요가 없음
  * 내장 톰캣을 사용해서 jar 파일만 있으면 바로 웹 애플리케이션 서버를 실행할 수 있음
  * 일반적으로 자바를 실행할 때는 java -jar라는 명령어를 사용하지만, 이렇게 하면 사용자가 터미널 접속을 끊을 때 애플리케이션도 같이 종료됨
  * 애플리케이션 실행자가 터미널을 종료해도 애플리케이션은 계속 구동될 수 있도록 nohup 명령어를 사용

와... 에러가 계속 나서 찾는 도중에, 쉘 스크립트에서 조건문을 사용할 때, if - else - fi 문을 사용시,

* if 띄고 \[ 띄고 조건문 띄고 ] 그리고 then이렇게 진행해야한다!!!!!!!

내 프로젝트에는 문제가 있다고 판단되서 그냥 진행하는 프로젝트 파일 말고 jojo님 블로그에서 프로젝트를 다운받아서 진행해봄...

만약 기회가 되면 밑의 블로그를 보면서 변경해보자!\~

최신 코드로 변경하기 : [https://jojoldu.tistory.com/539](https://jojoldu.tistory.com/539)
