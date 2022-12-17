# 응용 애플리케이션

음.. 다른건 모르겠지만 코틀린 봐본김에\
https://spring.io/guides/tutorials/spring-boot-kotlin/\
공식으로 제공해주고 있는 기본 SpringBoot + Gradle + Kotlin + Spring JPA로 구성\


하는건 일단 깃에 올리면서 하는게 맞는듯 : https://github.com/kyu9/JPA-Book-Practice

프로젝트의 설정에서\
groupId : 프로젝트 그룹명 artifactId : 프로젝트를 식별하는 아이디 version : 프로젝트 버전 name : 프로젝트 이름 packaging : 빌드, 패키징 방법

일단 application-properties를 제거하고 application.yml 으로 수정을 바꿔봤음\


```yaml
spring:
  datasource:
    driver-class-name: org.h2.Driver
    url: jdbc:h2:mem:testdb
    username: sa
    password:
  jpa:
    database-platform: H2
    hibernate:
      ddl-auto: create
    properties:
      hibernate:
        show_sql: true
        format_sql: true

logging:
  level:
    org.hibernate.sql: debug
```

\


swagger-ui를 여기를 참고\
https://www.baeldung.com/kotlin/swagger-spring-rest-api\
http://localhost:8080/swagger-ui/index.html 여기에서 확인 가능!\


h2를 기본으로 사용할 예정\
http://localhost:8080/h2-console 로 접속!!\
\


build.gradle.kts

```
import org.jetbrains.kotlin.gradle.tasks.KotlinCompile

plugins {
	id("org.springframework.boot") version "2.6.4"
	id("io.spring.dependency-management") version "1.0.11.RELEASE"
	kotlin("jvm") version "1.6.10"
	kotlin("plugin.spring") version "1.6.10"
	kotlin("plugin.jpa") version "1.6.10"
}

group = "jpabook"
version = "0.0.1-SNAPSHOT"
java.sourceCompatibility = JavaVersion.VERSION_1_8

repositories {
	mavenCentral()
}

dependencies {
	implementation("org.springframework.boot:spring-boot-starter-data-jpa")
	implementation("org.springframework.boot:spring-boot-starter-jdbc")
	implementation("org.springframework.boot:spring-boot-starter-mustache")
	implementation("org.springframework.boot:spring-boot-starter-web"){
		exclude(module= "spring-boot-starter-tomcat")
	}
	//undertow로 was 설정
	implementation("org.springframework.boot:spring-boot-starter-undertow")
	implementation("com.fasterxml.jackson.module:jackson-module-kotlin")
	implementation("org.jetbrains.kotlin:kotlin-reflect")
	implementation("org.jetbrains.kotlin:kotlin-stdlib-jdk8")
	implementation("org.projectlombok:lombok:1.18.22")
	developmentOnly("org.springframework.boot:spring-boot-devtools")
	implementation("com.h2database:h2")
	testImplementation("org.springframework.boot:spring-boot-starter-test")
	//Swagger
	implementation("org.springdoc:springdoc-openapi-data-rest:1.6.0")
	implementation("org.springdoc:springdoc-openapi-ui:1.6.0")
	implementation("org.springdoc:springdoc-openapi-kotlin:1.6.0")
	//logging
	implementation("io.github.microutils:kotlin-logging:1.12.5")

}

tasks.withType<KotlinCompile> {
	kotlinOptions {
		freeCompilerArgs = listOf("-Xjsr305=strict")
		jvmTarget = "1.8"
	}
}

tasks.withType<Test> {
	useJUnitPlatform()
}
```
