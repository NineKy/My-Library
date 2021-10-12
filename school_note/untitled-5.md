# Untitled

참고 : [https://www.betsol.com/blog/java-memory-management-for-java-virtual-machine-jvm/](https://www.betsol.com/blog/java-memory-management-for-java-virtual-machine-jvm/)

## JVM 이란? <a id="JavaMemory&#xAD6C;&#xC870;-JVM&#xC774;&#xB780;?"></a>

Java Virtual Machine의 약자로 컴퓨터가 자바 프로그램을 실행할 수 있도록 하는 추상 컴퓨팅 기계이다.

이는 코드를 불러오고, 코드를 검증하고, 코드를 실행하고, OS에서 할당받은 메모리 + 힙 압축 + 가비지 개체 제거를 포함한 Java 할당 관리, 런타임 환경\(Runtime Data Area\) 제공을 한다.

## JVM의 메모리 구조 <a id="JavaMemory&#xAD6C;&#xC870;-JVM&#xC758;&#xBA54;&#xBAA8;&#xB9AC;&#xAD6C;&#xC870;"></a>

자바 클래스 파일을 실행하게 되면 각 컴퓨터 OS로부터 메모리를 할당 받는다. 거기서 JVM이 돌아가기 시작하고, class파일들이 JVM위에서 해석되게 된다.

![](https://konawiki.konai.com/download/attachments/192958619/image.png?version=1&modificationDate=1625726392000&api=v2)

JVM이 메모리를 할당 받게 되면 Runtime Data Area가 할당 받게 된다.

Runtime Data Area은 크게 5가지 영역으로 나누게 된다.

Method Area, Heap Area, Stack Area, PC Register, Native Method Stack

**Heap Area**

객체를 저장할 때 사용하는 메모리 영역

처음에 JVM이 시작할 때 만들어지고, 크기는 키우고 줄이는 것이 가능하다. 그리고 가비지 컬렉션 전략에 의해서 크기가 고정된 크기일 수도 있고, 가변적인 크기일 수도 있다. 기본 값으로는 64MB 크기를 가지고 있다.

**Method Area**

메소드들의 바이트코드, static 변수, 런타임 상수 등이 위치하게 된다.

**Stack Area**

임시 값들이 생성되는 영역이다.  임시 값이라고 함은 메소드 인자, local 변수, 임시 변수 등을 의미한다. 각 클래스가 실행될 때 마다 새로운 Stack Area가 구성된다. 그리고 이 영역은 스레드당 하나씩만 존재하고 공유자원이 아니다.

| `public class Test {    static int tmp=10;    public static void main(String[] args) {        Person p = new` `Person();    }    static class Person{`             `}}` |
| :--- |


예시를 들어서 위의 코드가 실행되게 된다면, 

Method Area 에는 main\(\)에 대한 바이트 코드, Person에 대한 바이트 코드, 그리고 전역 변수인 tmp가 들어가게 된다.

Stack Area는 main이 실행되면서 생성되게 되고, Person 타입의 p변수, main의 인자인 args가 들어가게 된다.

Heap Area 에는 new 키워드를 통해서 생성한 Person이라는 객체가 들어가게 되고, Stack Area에 들어간 변수 p가 이곳의 객체를 참조하게 된다. 그리고 main의 인자인 args는 따로 가리키는 객체가 없기 때문에 null을 가리키게 된다.

**PC Register**

Program Counter의 줄인 말으로, 스레드당 하나씩 존재하고, 스레드가 실행될 때 지시를 이행한 후, 끝나면 다음 실행될 지시의 주소를 가리킴

**Native Method Stack** 

스레드와 네이티브 코드로 작성된 코드 사이를 매핑하는 역할을 수행

## Garbage Collection? <a id="JavaMemory&#xAD6C;&#xC870;-GarbageCollection?"></a>

자바 이전의 모든 프로그램의 메모리는 프로그래머가 알아서 관리해줘야 했다. 하지만 Garbage Collection 이라는 프로세스로 인해서 메모리 관리를 프로그래머가 하지 않아도 이게 알아서 관리를 해주게 되었다. 구체적으로는 Heap Area을 체크해서 불필요한 객체를 찾고, 제거하는 작업을 수행한다.

프로그램 상에서 더 이상 사용하지 않는 메모리를 **찾아서 알아서 제거해주는 역할**을 해주는 관리 프로세스라고 생각할 수 있다.

크게 3가지 단계로 작업이 이루어지게 된다

1. marking : gc가 어떤 객체가 사용되고 있고, 어떤 객체가 사용되지 않는지 찾는 단계
2. normal deletion : 사용되지 않는 객체를 제거하고, 남은 자리를 다른 객체들에게 할당하기 위해서 
3. deletion with compacting : 사용되지 않는 객체를 제거하고 나서, 살아있는 모든 객체의 위치를 다시 재조정 한다. 이 과정을 통해서 새로운 객체에게 메모리를 할당할 때 성능이 향상된다.

#### STW? <a id="JavaMemory&#xAD6C;&#xC870;-STW?"></a>

Stop-The-World는 GC가 실행되는 스레드를 제외한 나머지 모든 스레드를 멈추는 작업이다. GC가 작업을 완료하면 그때 다른 스레드들이 작업을 시작하게 된다. 

결과적으로 GC 실행을 위해서 애플리케이션을 멈추는 것이다. 추가적으로 GC튜닝이란 STW시간을 줄이는 것이다!

#### Garbage Collection 의 작동 원리 <a id="JavaMemory&#xAD6C;&#xC870;-GarbageCollection&#xC758;&#xC791;&#xB3D9;&#xC6D0;&#xB9AC;"></a>

참조

* [https://mirinae312.github.io/develop/2018/06/04/jvm\_gc.html](https://mirinae312.github.io/develop/2018/06/04/jvm_gc.html)
* [https://d2.naver.com/helloworld/1329](https://d2.naver.com/helloworld/1329)

JVM의 Runtime Data Area에 있는 Heap에서 작동하게 되는데 

Heap Area는 크게 2개의 물리적 공간으로 나뉘어져 있다. 그 두 공간은 Young Generation 영역과 Old Generation 영역이다.

Young Generation 영역 : 새롭게 생성한 객체의 대부분이 여기에 위치하게 된다. 대부분의 객체가 금방 접근 불가능 상태가 되기 때문에 많은 객체가 Young 영역에 생성되었다가 사라진다. 이 영역에서 객체가 사라질 때 Minor GC가 발생한다고 볼 수 있다.

Old Generation 영역 : Young Generation 영역에서 살아남은 객체가 여기로 복사된다. 대부분 Young Generation의 영역보다 더 크게 할당 되며, 크기가 큰 만큼 Young Generation 영역보다 GC가 적게 발생한다. 여기서 객체가 사라질 때 Full GC가 발생한다고 볼 수 있다.

Permanent Generation 영역 : Method Area 라고 하며, 객체나 억지로 머물게 된 문자열 정보를 저장하는 곳이며, Old 영역에서 살아남은 객체가 영원히 남아 있는 곳은 절대 아니다. 그리고 이곳은 기본적으로 데이터가 가득 차면 GC를 실행한다.

Young Generation 영역 구성

= Eden + Survivor 1 + Survivor 2

new 키워드 등을 사용해서 새롭게 생성한 객체의 대부분은 Eden 영역에 위치하게 된다.

Eden 영역에서 GC가 발생하고 살아남은 객체는 Survivor 1, 2 중 하나로 이동하게 된다.

만약 Survivor 영역 중 하나가 가득 차게 된다면, 살아있는 객체를 다른 Survivor 영역으로 이동시킨다. 그리고 가득 찬 Survivor 영역에는 아무런 데이터도 없는 상태로 된다.

이렇게 반복하면서 계속 살아있는 객체는 Old Generation 영역으로 이동 시킨다.

![](https://konawiki.konai.com/download/attachments/192958619/JVMObjectLifecycle.png?version=1&modificationDate=1626051529000&api=v2)

GC 종류

* Serial GC - mark-sweep-compaction 알고리즘을 사용한다. 1단계로 mark으로 살아있는 객체를 식별, 2단계로 Heap Area의 앞 부분부터 확인하여 살아 있는 것만 남기고\(Sweep\) 3단계로 각 객체들이 연속되게 쌓이도록 Heap의 가장 앞 부분부터 채워서 객체가 존재하는 부분과 객체가 없는 부분으로 나눈다\(Compaction\). 하지만 데스크톱의 CPU코어가 하나만 있을 때 사용하기 위해서 만든 방식이기 때문에 운영 서버에서는 절대 사용하면 안 되는 방식
* Parallel GC - Serial GC와 같은 알고리즘을 사용하지만 GC를 처리하는 스레드가 여러 개이다 / Java 8에서 default GC
* Parallel Old GC - mark-summary-compaction 알고리즘을 사용한다. 
* Concurrent Mark & Sweep GC \(CMS\) - STW 시간이 매우 짧다. 다른 GC보다 메모리와 CPU를 많이 사용

1. Inital Mark : 클래스 로더에서 가장 가까운 객체 중 살아 있는 객체만 찾고 끝냄
2. Concurrent Mark : 방금 살아있다고 확인한 객체에서 참조하고 있는 객체들을 따라가면서 확인\(다른 스레드가 실행 중인 상태에서 동시에 진행\)
3. Remark : Concurrent mark 단계에서 새로 추가되거나 참조가 끊긴 객체를 확인
4. Concurrent Sweep : 쓰레기를 정리하는 작업

* G1\(Garbage First\) GC 
  * 바둑판 영역 형태로 객체가 각 영역\(Region\)에 할당되는 형태이다. 그러다가 만약 해당 영역이 가득 차게 되면, 다른 영역에 객체를 할당하고 GC를 실행 / Java 9부터 기본 GC
  * 기존에는 Young의 Eden, Survivor, Old 이렇게 영역들이 나뉘어져서 존재하고 있었지만 G1 GC 방식을 사용하게 되면 각 Region의 상태에 따라서 Eden, Survivor, Old이 동적으로 역할을 부여해주는 방식이다.
  * Region의 역할에는 Eden, Survivor, Old 이외에도 Humongous, Available/Unused 방식이 존재한다.
    * Humongous는 Region 크기의 50%을 초과하는 큰 객체를 저장하기 위한 공간이며, GC동작이 최적으로 동작하지 않는다.
    * Available / Unused는 아직 사용되지 않은 Region을 의미한다.
  * G1 GC에서 Minor GC가 실행되면 STW가 발생하고 이를 최대한으로 줄이기 위해서 멀티스레드로 GC를 수행
    * Minor GC는 Region들 중에 GC대상 객체가 가장 많은 Region에서 수행되고, 여기서 살아남은 객체를 다른 Region으로 옮기고 빈 Region을 Available/Unused Region으로 돌리는 과정으로 수행된다.
  * G1 GC에서 Full GC

  1. Inital Mark : Old Region 에 존재하는 객체들이 참조하는 Survivor Region을 찾고, 여기서 STW가 발생
  2. Root Region Scan : Inital Mark에서 찾은 Survivor Region에 대한 GC 대상 객체를 찾는다.
  3. Concurrent Mark : 전체 Region을 스캔해서 GC 대상 객체가 없는 Region은 이후 단계에서 제회
  4. Remark : 애플리케이션을 멈추고\(STW\) 최종적으로 GC 대상에서 제외할 객체를 식별
  5. Cleanup : 애플리케이션을 멈추고\(STW\) 살아있는 객체가 가장 적은 Region에서 사용하지 않는 객체를 제거한다. STW를 끝내고 빈 Region을 Freelist에 추가해서 재사용될 수 있게 한다.
  6. Copy : 완전히 비워지지 않은 Region의 살아남은 객체들을 새로운 Region에 복사해서 Compaction 작업을 수행

#### Parallel GC vs G1 GC? <a id="JavaMemory&#xAD6C;&#xC870;-ParallelGCvsG1GC?"></a>

Parallel은 JVM의 기본 GC이다.  이 것의 최대 장점은 다수의 스레드를 사용한다는 점이다. 하지만 minor GC 나 full GC가 일어나게 되면 애플리케이션이 멈춘다는 것이다. 그래서 Parallel의 가장 적합한 애플리케이션은 일시 정지를 허용할 수 있고, CPU 오버헤드를 낮추기 위해 최적화된 앱에 가장 적합

G1 GC는 반대로 STW를 최대한 줄일 수 있다는 장점을 가지고 있다.

Stack Overflow : 스택에 있는 메모리가 영향을 받는 것이다. 지역변수의 값, 함수의 인자 값, 리턴하는 주소들이 영향을 받는다.

Heap Overflow : Heap Area에 존재하고 있는 메모리들이 넘쳐서 문제가 생기는 것이다. 전역변수나 다른 프로그램 데이터가 영향을 받는다.

