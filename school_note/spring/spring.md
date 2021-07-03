# Spring이란?

### Spring Framework?

Spring Framework은 전자정부 표준 프레임워크로, 개발할 때 설계의 기본이 되는 구조를 제공하는 서비스이다.



Spring은 Light Weight Java Application Framework이다. 즉, App 구조로써 코드의 상당부분을 제공해준다. 그래서 핵심 로직\(비지니스 로직\)에만 집중할 수 있는 것이 장점이다. 그리고 POJO\(Plain Old Java Object\) 기반의 엔터프라이즈이기 떄문에 App의 개발을 쉽고 편하게 할 수 있다는 점이 장점이다.



### Spring의 특징

#### POJO 방식의 프레임워크

POJO란 Plain Old Java Object로, 따로 인터페이스를 구현하거나 상속을 받을 필요가 없어서 객체자체가 가볍다.

#### Inversion of Control\(IoC\) : 제어의 역행

제어의 역행이란, 모든 객체에 대한 제어를 개발자가 하는 것이 아니라 프레임워크에게 넘긴다는 의미이다. 개발자는 이 특징으로 인해서 생성해야하는 비지니스 로직에만 집중할 수 있다.

#### Dependency Injection\(DI\) : 의존성 주입

서비스간, 계층간 의존성이 존재할 경우에는 프레임워크가 서로를 연결시켜준다. 이를 통해서 의존성은 줄어들게 되고, 그 의미는 변화에 용이하게 된다는 장점을 가져온다.

의존성이 있는 컴포넌트들의 관계를 개발자가 직접 코드로 명시하지 않고, 컨테이너인 Spring이 런타임에 찾아서 연결해주는 것

#### Aspect-Oriented Programming\(AOP\) : 관점 지향 프로그래밍

여러 개에 거쳐 있는 기능들을 독립적으로 빼서 사용하는 것이 가능하다. 이를 통해서 재사용성을 보장받을 수 있다는 장점이 있다.



### Spring Container?

Container란 인스턴스의 생명 주기를 관리하고, 생성된 인스턴스에게 추가적인 기능을 제공하도록 하는 것이다. 생명 주기를 관리하기 때문에 객체의 생성과 소멸을 관리한다고 생각하면된다.

container는 Bean을 생성, 관리, 제거한다. 즉, Container가 Bean을 관리하고 있다는 것이라고 생각하면 된다.

그러면 Spring Container는 뭘까?

자바 객체를 담고 있으며, 언제나 필요할 때 여기서 객체를 가져와서 사용할 수 있고, Bean들의 생명주기를 관리하는 역할을 하는 애이다.

IoC을 사용하는데 : 개발자는 New 연산자, 인터페이스 호출, 팩토리 호출 방식으로 객체를 생성하고 소멸시키는데, 여기서 인스턴스의 생성부터 소멸까지의 객체 생명주기 관리를 스프링이 해준다.

스프링 컨테이너에는 두 가지의 종류가 있다.

1. BeanFactory
   * bean 으로 등록, 생성, 조회, 돌려주기 그외의 부가적으로 bean을 관리하는 기능을 담당
2. ApplicationContext
   * beanfactory을 상속하고 확장해서 만든 컨테이너이고 추가적인 기능들을 가지고 있다.

{% embed url="https://jobjava00.github.io/language/java/framework/spring/container/" %}

위에서 참고해서 작성



### Spring MVC?

M\(Model\), V\(View\), C\(Controller\)을 제공해준다는 의미

* Model : 웹 애플리케이션의 데이터\(POJO\)
* View : model을 rendering 해주는 역할
* Controller : 요청을 처리해주고 결과를 모델 객체에 붙혀서 view에서 보여줌

구조를 생각해보면

1. Dispatcher Servlet : 모든 request를 가로채서 적절하게 분배
2. Handler Mapping : @RequsetMapping을 통해서 적절한 Controller에게 보낸다.
3. Controller : 요청을 처리해주고, 결과를 모델에 붙혀서 view에게 보여줌
4. View Resolver : 이름을 통해서 파일을 찾아주는 역할
5. View : JSP, Mustache, HTML등

이렇게 나눠질 수 있다!



### Spring Module?

{% embed url="https://goddaehee.tistory.com/156" %}

이 글을 보면서 본인이 한번씩은 다 사용해본 모듈이기에 참조했다.

1. Spring Core

   그냥 Spring의 중심 역할을 하는 요소이고, IoC 기능을 지원한다.

2. Spring Context

   Spring Core의 바로 상위에 존재하고, Spring Core보다 더 추가적인 기능들을 가지고 있다.

3. Spring DAO

   DAO란 Data Access Object의 줄인말이다. DAO는 관계형 데이터베이스에 접속하기 위한 객체 지향적 API라고 생각하면 된다. 이 모듈을 사용해서 DAO개발을 좀 더 쉽고, 일관된 방법으로 개발할 수 있게 된다.

4. Spring ORM

   ORM이란 Object Relation Mapping 으로, Hibernate와 같은 것과 통합이 쉬워진다. 여기서 orm이란 객체와 관계형 데이터베이스의 데이터를 자동으로 매핑해주는 것을 의미한다.

5. Spring AOP

   AOP을 사용하기 위해서 필요

6. Spring Web

   Web 애플리케이션을 만들기 위해서 필요한 기능들을 지원\(Web Application Context등\)









