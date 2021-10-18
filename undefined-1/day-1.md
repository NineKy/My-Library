# Day 1

## TDD란?

👉 테스트가 주도하는 개발

항상 실패하는 테스트를 먼저 작성하고(Red),

테스트가 통과하는 프로덕션 코드를 작성하고(Green),

테스트가 통과하면 프로덕션 코드를 리팩토링(Refactor)

테스트 코드의 작성을 도와주는 프레임워크 : xUnit

👉Why? 테스트 코드

위키피디아

* 단위 테스트는 개발단계 초기에 문제를 발견하게 도와줍니다
* 단위 테스트는 개발자가 나중에 코드를 리팩토링하거나 라이브러리 업그레이드 등에서 기존 기능이 올바르게 작동하는지 확인할 수 있습니다
* 단위 테스트는 기능에 대한 불확실성을 감소시킬 수 있습니다
* 단위 테스트는 시스템에 대한 실제 문서를 제공합니다. 즉 단위 테스트 자체가 문서로 사용될 수 있습니다

경험담

* 빠른 피드백
* System.out.println()을 통해서 눈으로 검증해야하는 문제 ⇒ 테스트코드가 자동 검증해줌
* 개발자가 만든 기능을 안전하게 보호

👉실제로 테스트 코드 작성해보기

패키지 생성시, 패키지 명은 웹 사이트 주소의 역순으로 지음

[admin.jojoldu.com](http://admin.jojoldu.com)의 사이트의 패키지 명 ⇒ com.jojoldu.admin으로 설정

👉작성한 테스트 코드

```java
package com.kyu.book.springboot;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

Application이 앞으로의 메인 클래스

@SpringBootApplication : 스프링 부트의 자동설정, 스프링 bean 읽기/생성을 모두 자동으로 설정

* @SpringBootApplication annotation이 있는 위치부터 설정을 읽음 ⇒ 이 클래스는 항상 프로젝트의 최상단에 위치해야함

psvm에 있는 SpringApplication.run()으로 내장 WAS(Web Application Server)가 실행됨(별도의 톰캣 설치가 불필요)

```java
package com.kyu.book.springboot.web;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {
    @GetMapping("/hello")
    public String hello(){
        return "hello";
    }
}
```

@RestController

* 컨트롤러를 JSON을 반환하는 컨트롤러로 만들어준다

@GetMapping

* HTTP Method인 Get의 요청을 받을 수 있는 API를 만들어 줍니다
* 과거에는 @RequestMapping으로 사용되었었음
