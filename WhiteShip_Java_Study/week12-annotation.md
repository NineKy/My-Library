# Week12\(Annotation\)

## 애노테이션이란

Annotation은 _주석_ 이라는 의미를 가지고 있다. 하지만 /나 /\*와 같은 주석과는 확실히 다르다

* 컴파일 과정에서 코드를 어떻게 컴파일 할 것인지, 실행 과정에서 코드를 어떻게 처리할 것인지를 알려주는 정보
* annotation이 붙은 코드는 annotation에 관한 정보가 구현된 것에 따라서 연결되는 방향이 결정된다
* 전체 소스코드에서 비지니스 로직에는 영향을 주지는 않지만 해당 타겟의 연결 방법이나 소스코드의 구조를 변경할 수 있다.

  = 이 속성을 어떤 용도로 사용할껀지, 이 클래스에게 어떤 역할을 줄껀지를 결정해서 붙여주는 것

* annotation은 소스코드에 메타데이터를 삽입하는 것이기 때문에 잘 이용한다면 구독성 뿐만 아니라 체계적인 소스코드를 구성하는데 도움을 준다
  * 메타 데이터란 -&gt; 데이터를 위한 데이터를 의미하며, 데이터에 대한 설명을 의미하는 데이터\(자신의 정보를 가지고 있는 데이터\)

### 사용법

패키지명이나 클래스 선언, 메소드 선언, 필드 선언, 인자값 선언 위에다가

@이름\(인자\)

방식으로 넣어주고 만약 인자가 없을경우에는 \(인자\) 부분은 빼고 사용하는 것도 가능하다

### 선언방법

```java
@Retention()
@Target()
public @interface ExAnnotation{
  //타입 요소명() default값;
  String value(); // String형 기본 요소
  int value2() default 5; //int형 요소 초기값 5
}
```

이렇게 annotation은 멤버를 가지는 것이 가능하며, 타입, 이름, dafault값을 설정하는 것이 가능하다

만약 default값을 따로 정해주지 않는다면 기본 요소가 된다

### 사용방법

```java
@ExAnnotation(value = "Hello", value2 = 1);
```

```java
@ExAnnoation("Hello");
```

* 만약 annotation 안에 있는 요소의 값에 default값이 설정되어 있다면, 값을 따로 설정하지 않아도 되지만\(int\) default값이 없는 경우에는 값을 설정해줘야 함\(String\)

## 표준 Annotation

```text
@FunctionalInterface - Specifies that the type declaration is intended to be a functional interface, since Java 8.
```

* @Override : 메소드가 override되었는지 확인하고 만약 부모 클래스나 구현된 인터페이스에서 메소드를 찾지 못했을 때 컴파일 경고를 발생시킨다
* @Deprecated : 메소드에 마킹을 해두고 만약 메소드가 사용되었을 때 컴파일 경고를 줌
* @SafeVarargs : 자바 7부터 가변인자 매개변수를 사용한 메소드나 생성자를 부르는 것들의 경고들을 무시
* @SurppressWarnings : annotation 인자들 안의 컴파일 시의 경고를 컴파일러로 하여금 무시하도록 함
* @FunctionalInterface : 자바 8부터 기능적 인터페이스가 되기 위해 의도된 타입 선언을 구체화함

## @Retention이란

어느 시점까지 Annotation의 메모리를 가져갈 건지 설정하는 부분

조금 더 알아보기 위해서 내부를 들여다 보았다

```java
package java.lang.annotation;

/**
 * Indicates how long annotations with the annotated type are to
 * be retained.  If no Retention annotation is present on
 * an annotation type declaration, the retention policy defaults to
 * {@code RetentionPolicy.CLASS}.
 *
 * <p>A Retention meta-annotation has effect only if the
 * meta-annotated type is used directly for annotation.  It has no
 * effect if the meta-annotated type is used as a member type in
 * another annotation type.
 *
 * @author  Joshua Bloch
 * @since 1.5
 * @jls 9.6.3.2 @Retention
 */
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Retention {
    /**
     * Returns the retention policy.
     * @return the retention policy
     */
    RetentionPolicy value();
}
```

annotation 유형이 있는 annotation이 보존되는 기간을 나타냄

인자로는 RetentionPolicy가 들어가게 된다

인자로 들어가는 RetentionPolicy를 알아보기 위해 다시 또 내부를 들여다봤다

```java
package java.lang.annotation;

/**
 * Annotation retention policy.  The constants of this enumerated type
 * describe the various policies for retaining annotations.  They are used
 * in conjunction with the {@link Retention} meta-annotation type to specify
 * how long annotations are to be retained.
 *
 * @author  Joshua Bloch
 * @since 1.5
 */
public enum RetentionPolicy {
    /**
     * Annotations are to be discarded by the compiler.
     */
    SOURCE,

    /**
     * Annotations are to be recorded in the class file by the compiler
     * but need not be retained by the VM at run time.  This is the default
     * behavior.
     */
    CLASS,

    /**
     * Annotations are to be recorded in the class file by the compiler and
     * retained by the VM at run time, so they may be read reflectively.
     *
     * @see java.lang.reflect.AnnotatedElement
     */
    RUNTIME
}
```

* RetentionPolicy값을 넣어주면 그것으로 annotation의 메모리 보유 범위가 결정된다
* 요소
  * SOURCE : 해당 annotation의 메모리를 컴파일러가 버린다
  * CLASS : 컴파일러가 컴파일에서는 annotation의 메모리를 가져가지만 실제론 런타임시에 사라진다
  * RUNTIME : 컴파일러에 의해서 클래스 파일에 기록되고, 런타임 시 JVM에 의해 유지된다. 

```java
@Retention(RetentionPolicy.RUNTIME)
```

이렇게 사용하고 RUNTIME 자리에 필요한 요소들을 넣어서 사용가능

## @Target이란

annotation이 적용될 위치를 결정

이것도 내부를 들여다보면 이렇게 설명해두었다.

```java
package java.lang.annotation;

@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Target {
    /**
     * Returns an array of the kinds of elements an annotation type
     * can be applied to.
     * @return an array of the kinds of elements an annotation type
     * can be applied to
     */
    ElementType[] value();
}
```

위에서의 Retention 처럼 ElementType의 요소들을 원하는 만큼 넣을 수 있는 것 처럼 보인다

다시 ElementType을 찾아보자

```java
package java.lang.annotation;

public enum ElementType {
    /** Class, interface (including annotation type), or enum declaration */
    TYPE,

    /** Field declaration (includes enum constants) */
    FIELD,

    /** Method declaration */
    METHOD,

    /** Formal parameter declaration */
    PARAMETER,

    /** Constructor declaration */
    CONSTRUCTOR,

    /** Local variable declaration */
    LOCAL_VARIABLE,

    /** Annotation type declaration */
    ANNOTATION_TYPE,

    /** Package declaration */
    PACKAGE,

    /**
     * Type parameter declaration
     *
     * @since 1.8
     */
    TYPE_PARAMETER,

    /**
     * Use of a type
     *
     * @since 1.8
     */
    TYPE_USE
}
```

* TYPE : 타입\(클래스, 인터페이스, enum\) 선언 시 사용
* FIELD : 멤버 변수 선언 시 사용\(enum 포함\)
* METHOD : 메소드 선언 시 사용
* PARAMETER : 매개 변수 선언 시 사용
* CONSTRUCTOR : 생성자 선언 시 사용
* LOCAL\_VARIABLE : 지역 변수 선언 시 사용
* ANNOTATION\_TYPE : Annotation type 선언 시
* PACKAGE : 패키지 선언 시
* TYPE\_PARAMETER : 변수 타입 선언 시
* TYPE\_USER : 타입 사용 시

## @Documented이란

형태를 가지는 annotation이 javadoc 같은 툴에 의해서 default로 문서화 되는 것을 나타냄

=문서에 정보가 표현된다라고 생각하면 될듯하다

```java
package java.lang.annotation;

/**
 * Indicates that annotations with a type are to be documented by javadoc
 * and similar tools by default.  This type should be used to annotate the
 * declarations of types whose annotations affect the use of annotated
 * elements by their clients.  If a type declaration is annotated with
 * Documented, its annotations become part of the public API
 * of the annotated elements.
 *
 * @author  Joshua Bloch
 * @since 1.5
 */
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Documented {
}
```

## 애노테이션 프로세서

### Annotation Processing이란? \(from Stackoverflow\)

-&gt; 자바 컴파일러의 컴파일 단계에서 유저가 정의한 annotation의 소스코드를 분석하고 처리하기 위해 사용되는 훅이다. 컴파일 에러나 컴파일 경고를 만들어내거나 소스코드\(.java\)와 바이트코드\(.class\)를 내보내기도 한다

이것의 가장 대표적인 예시는 lombok가 있으며

장점은 런타임 비용이 안든다는점이고

단점은 기존의 클래스 코드를 변경할 때는 내부 클래스를 사용해서 기존 코드를 만지기 때문에 해킹에 비슷하다

annotation process를 직접 작성하거나 만들어진 annotation processor를 확인하면 Abstract Processor 클래스를 상속받는 것을 알 수 있다.

## 참고사이트

[https://www.nextree.co.kr/p5864/](https://www.nextree.co.kr/p5864/)

[https://coding-factory.tistory.com/575](https://coding-factory.tistory.com/575)

[http://cris.joongbu.ac.kr/course/java/api/java/lang/annotation/Documented.html](http://cris.joongbu.ac.kr/course/java/api/java/lang/annotation/Documented.html)

[https://yadon079.github.io/2021/java study halle/week-12](https://yadon079.github.io/2021/java%20study%20halle/week-12)

[https://en.wikipedia.org/wiki/Java\_annotation](https://en.wikipedia.org/wiki/Java_annotation)

