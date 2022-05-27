# Gradle

## Gradle이란?

참조 : [http://www.devkuma.com/books/pages/1064](http://www.devkuma.com/books/pages/1064)

[https://kwonnam.pe.kr/wiki/gradle/buildlifecycle](https://kwonnam.pe.kr/wiki/gradle/buildlifecycle)

위키백과에는 Gradle이란 그루비\(Groovy\)을 이용한 빌드 자동화 시스템이라고 적혀있다.

* 여기서 그루비\(groovy\)란, 자바에 파이썬, 루비 등 여러가지 언어들의 특징을 더한 동적 객체 지향 프로그래밍 언어라고 생각하면 된다. 그래서 Groovy는 JVM에서 실행되는 스크립트 언어라고 정의할 수 있다.

원래 사용하던 빌드 툴인 Ant나 Maven을 제치고 Gradle을 사용하는 이유는 **역시 기존의 빌드 툴들의 단점을 보완하고 장점만 빼서 사용했기 때문이다.**

**Maven보다 Gradle을 사용하는 이유**

* 가독성 : 태그 방식으로 구구절절 작성하다 보니, 양이 많아질 수록 알아보기 힘들다, 의존 관계가 복잡해지면 쉽게 파악하기 힘들다.
* 특정 설정을 소수의 모듈에서 공유하기 위해서는 부모 모듈을 생성해서 상속 받아야 하는 maven의 단점을 보완
* Groovy 언어를 사용하기 때문에 동적인 빌드도 원하면 따로 작성하는 것이 가능
* Gradle은 작업 의존성 그래프를 기반으로 하는 반면 Maven은 선형적인 단계의 모델을 기반으로 하기 때문에 속도 차이도 많이 난다! \(약 100배\)

Build라는 단어가 계속해서 나오는데 프로젝트를 build 한다는 의미는 소스 코드 파일을 컴퓨터나 휴대폰에서 실행할 수 있는 독립 소프트웨어로 만들어주는\(Jar, War 파일 등으로\) 과정을 의미한다. 

**Build phase**

1. Initialization : gradle에서는 단일, 멀티 프로젝트를 모두 지원하기 때문에 이 단계에서 어떤 프로젝트를 빌드할 것인지 결정하고 프로젝트 객체를 만드는 단계이다.
2. Configuration : 빌드된 프로젝트들의 빌드 스크립트를 실행하고 프로젝트 객체를 구성한다.
3. Execution : gradle은 configuration 단계에서 생성 및 구성된 부분 tasks들을 결정한다. 그리고 결정된 tasks들을 실행한다.

**Java Plugin tasks간 의존관계**

![](https://konawiki.konai.com/download/attachments/192953800/gradle%20build%20lifecycle.png?version=1&modificationDate=1625707987000&api=v2)

Gradle은 Maven이 가지는 상속문제를 해결하기 위해서 configuration을 통해서 확장을 선택했다. 이 확장은 plugins에 의해서 가능하게 되었다.

Gradle은 plugins에 값들을 추가하면서 빌드에 필요한 task가 추가될 뿐만 아니라 task간의 의존관계까지 설정함으로써 빌드 작업을 수행



### 프로젝트에서의 Gradle 구조

Gradle을 선택해서 프로젝트를 생성하게 되면 구조는 크게 이렇다.

* build.gradle
* gradle
  * wrapper
    * gradle-wrapper.jar
    * gradle-wrapper.properties
* gradlew
* gradlew.bat
* settings.gradle
* src
  * main
  * test

각 파일이 무엇을 의미하는지 확인해보자

#### **build.gradle이란?**

Gradle의 기본 빌드 설정 파일이고. project 빌드 처리에 대해서 내용을 작성하는 곳이다.

build.gradle을 빌드 스크립트라고도 하며 빌드를 구성하고 있는 의존성이나 플러그인 등과 같은 설정들을 해주는 곳이다.

= 가장 많이 사용하게 될 파일이다.

초기의 build.gradle 모습



#### build.gradle의 내부 태그

**plugins?**

Gradle 플러그인을 사용해서 프로젝트의 기능을 늘리는 것이 가능하다. java나 spring 관련 키워드들을 넣어준다.

기존에는 apply plugin : 'java' 이런 식으로 했었는데 

plugins 블록안에 값을 넣어주는 방식으로 구조가 더욱 쉽게 바뀌었다.

'java' : Java 프로그램을 위한 기능을 제공하는 플러그인 → gradle compile은 이걸 추가해서 가능

'application' : 응용 프로그램에 대한 기능을 제공하는 플러그인 → gradle run은 이걸 추가해서 가능



**repositories?**

Gradle은 library를 자동으로 다운로드하고 통합한다. 그렇기 때문에 build.gradle 안에서 '_어떤 저장소를 사용하는지_' 명시를 해줘야 한다.

로컬 저장소를 사용할 수 있고, 원격 저장소를 사용할 수 있다. 

원격 저장소의 대표적으로 mavenCentral\(\)을 사용하고 이는 Apache Maven 중앙 저장소를 의미한다.

#### \*\*\*\*

**dependencies?**

여기에 프로젝트에서 사용하고자 하는 library들을 명시해준다. 여기에 명시를 해주면 repositories에 명시해둔 저장소에서 필요한 library를 사용할 수 있게 되는 것이다.

configuration으로 4가지가 존재한다.

* compile : 프로젝트가 컴파일 될 때,
* runtime : 프로젝트를 실행할 때 → compile을 모두 포함
* testCompile : 프로젝트의 테스트가 컴파일 될 때 → 프로젝트의 컴파일된 클래스들과 compile lib의 의존성 포함
* testRuntime : 프로젝트의 테스트를 실행할 때  → compile, runtime, testCompile의 의존성 포함

implementation?

참조 : [https://bluayer.com/13](https://bluayer.com/13)

compile은 애초에 Gradle 3.0 이후부터는 권장하지 않는다고 한다. 

compile 과 차이를 확인해보면 

compile 하면 연결된 API 모두가 프로젝트에 의해서 노출되지만 implementation은 연결된 API가 노출되지 않는다는 점이다.

또한 의존하는 모듈이 이렇게 있다고 가정해보자

![](https://konawiki.konai.com/download/attachments/192953800/imple.png?version=1&modificationDate=1626046706000&api=v2)

A가 B를 의존하고 있고, B가 C를 의존하고 있는 상황이다. = A → B → C

만약 가장 하위에 있는 A 모듈이 수정되게 되면, 

compile 사용 시, A를 직접, 간접 의존하고 있는 B와 C 모두 rebuild 되지만

implementation 사용 시, A를 직접 의존하고 있는 B만 rebuild된다.

=&gt; 결론 : 모듈이 많으면 많을수록 compile을 사용했냐 implementation을 사용했냐 로 build 속도에 차이가 난다.

#### gradlew? gradlew.bat?

gradle은 굳이 java나 gradle이 없더라도 빌드할 수 있도록 되어있다. 그것을 가능하게 해주는 것이 wrapper이고 위의 gradlew는 gradle + wrapper 이렇게 되어있는 것이다.

* wrapper란? : 운영체제에 맞춰서 gradle 빌드를 수행하도록 하는 배치 스크립트
  * 배치 스크립트? : 명령 프롬프트의 명령들을 하나의 일괄\(Batch\)로 처리해서 자동화 해주는 파일

그냥 실행시키는 것으로 빌드할 수 있고, 종류로는 2가지가 있다.

gradlew은 linux나 mac이 사용하면 된다. 그리고 gradlew.bat은 윈도우가 사용하면 된다.

#### settings.gradle

프로젝트 구성에 있어서 설정해야 하는 것들이 있으면 수정해야 하는 파일

이곳에서 멀티 프로젝트 빌드가 가능하다. 멀티 프로젝트 빌드는 Gradle을 한 번 실행하는 동안 하나 이상의 프로젝트를 빌드한다. 

최상위 프로젝트의 setting.gradle에서 하위 프로젝트들을 넣어준다.  → 트리 방식의 프로젝트 구조를 가질 시 사용

만약 하나의 task를 실행 시, 하위 프로젝트에 해당 task가 있다면 최상위 프로젝트를 시작으로 하위 프로젝트에 있는 task도 실행된다.

#### src

크게 main폴더와 test폴더로 나뉘게 되는데,

main에는 기본적으로 코드들을 작성해서 넣어주고

test에는 main의 코드들의 단위 테스트를 진행할 코드들을 작성해서 넣어주면 된다.

### 

### Gradle Task?

Task란 Gradle의 작업 단위이고 gradle에서 사용하는 groovy 문법을 사용한다. 선언은 build.gradle 안에서 선언을 해주고, intellij 기준으로 오른쪽 gradle칸 → Tasks → other에서 사용자가 정의한 task을 찾을 수 있다.

그리고 실행할 때는 gradle 'task' 이 순서대로 진행하면 task가 수행된다.

ex\) 프로젝트를 실행 "gradle bootrun" 이렇게 사용했었는데 이러한 경우는 이미 내부에서 task로 선언이 되어 있기 때문에 실행이 되는 것이다.

**미리 선언되어 있고, 사용 빈도가 있을 만한 그런 Task**

init : 폴더에 프로젝트 파일이나 폴더들을 생성하고 폴더를 초기화한다. 

compileJava : Java 소스 코드를 컴파일하기 위한 Task

run : 자바 클래스나 프로젝트\(서버\)등을 실행, 즉 main 클래스를 실행시키는 Task

jar : 클래스와 패키지를 엮어서 jar 파일로 만드는 Task → 만들어진 파일은 build/libs에 저장된다!

clean : 프로젝트를 빌드 시, build 폹더 안에 여러가지 파일들이 저장되게 되는데, 이 파일들을 제거한다! 프로젝트가 꼬였을 때, 이걸 사용하면 프로젝트를 refresh하는 것이 가능하다!

gradle Task을 사용해서 프로젝트 빌드, 실행 하기 위한 단계

gradle compileJava → gradle run → gradle jar : 프로그램의 컴파일을 진행 → 프로젝트 main 실행 → 프로젝트의 jar 파일을 생성

**SpringBoot에서 사용할만한 Task**

bootRun : springboot 프로젝트를 실행시키는 Task

bootJar / bootWar : jar / war로 패키징을 해주는 Task

#### 그러면 어떻게 커스텀해서 task을 만들 수 있을까?

**task의 선언**

|  |
| :--- |


사용한 예시

|  |
| :--- |


실행하기 위해서 cmd에서 gradle hello을 입력하면 아래와 같은 결과물을 확인할 수 있다

![](https://konawiki.konai.com/download/attachments/192953800/image2021-7-6_12-26-41.png?version=1&modificationDate=1625542002000&api=v2)

성공적으로 println 'THIS IS A TEST!!!!!' 이 출력 된 것을 확인할 수 있다.

**doFirst, doLast을 통한 task 수행 순서 조정**

|  |
| :--- |


이렇게 새로운 task 을 만들어보았다.

doFirst 블럭 안에 들어가는 문장이 가장 먼저 출력되야하고, doLast 블럭 안에 들어가는 문장이 가장 마지막에 출력되어야한다.

결과적으로는 First / sequence / Last 순서대로 출력해야함!

실제로 실행한 결과도 잘 나오는 것을 확인할 수 있다. =&gt; -q 옵션은 결과물만 출력할 수 있도록\(보기 편하게\) 다른 출력들을 제거해주는 역할

![](https://konawiki.konai.com/download/attachments/192953800/image2021-7-6_12-45-24.png?version=1&modificationDate=1625543125000&api=v2)

**task에서 매개변수를 사용하는 방법**

실행 방법은 그냥 task를 실행하는 것과 동일하지만, 변수는 뒤에 -P변수명=변수값 이러한 형태를 붙여줘야 한다.

테스트를 위해 새로운 코드를 만들었다.

|  |
| :--- |


실행할 때는 gradle 태스크명 -P변수명=변수값

이러한 방식을 사용해서 변수를 넣어주면 성공적으로 결과가 나온다



**Task안에서 다른 Task을 실행하는 방법**

tasks.호출할task이름.execute\(\)

이렇게 하면 다른 task을 부르는 것이 가능하다.

|  |
| :--- |


이렇게 해주면 after task의 doFirst에 의해서 bbb가 먼저 실행되고 다음으로 after가 실행된다.







