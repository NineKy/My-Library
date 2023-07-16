# ElasticSearch 코드 적용기 
실제로 코드의 적용은 우선은 회사의 개발망에서 토이 프로젝트로 작업했습니다 <br>
spring boot 버전은 2.6.5 를 기준으로 작업 진행했습니다 <br>
<br><br>

## build.gradle 을 통해서 spring-data-elasticsearch 적용

<br><br>


## elasticsearch 접속을 위한 configuration 설정

빌드 도구에 원하는걸 설정하고 나서는 직접 설정을 해주어야 합니다 <br>
ES에 붙기 위해서 설정하는 방법은 여러가지가 있습니다 <br>
다양한 방법 중에 가장 중요한 사항은 <br>
RestHighLevelClient (ES와 통신하기 위해 사용하는 client 객체) 를 빈으로 등록해서 자동으로 해당 객체로 통신하도록 등록하는 것입니다 <br>
간단하게 확인할 수 있습니다 <br>

<br>
해당 빈에서 통신과 관련해서 필요한 정보들을 세팅 가능합니다(타임아웃시간, 인증서, id, pwd 등)

<br>
@EnableElasticsearchRepositories 을 통해서 elasticsearch 으로 접근하는 ElasticsearchRepository 의 사용을 선언

<br><br>

spring-data-elasticsearch 을 사용함으로써 정말 간편하게 사용하는 것이 가능합니다 <br>
@EnableElasticsearchRepositories 을 통해서 기존에 사용하던 JpaRepository 처럼 ElasticsearchRepository 를 상속을 통해서 유사하게 사용합니다
<br>


elasticsearch 에 저장하기 위한 인덱스 객체 선언합니다(jpa 에서 Entity 역할) <br>
@Document(indexName = "저장하고자 하는 인덱스이름") 을 통해서 인덱스를 명시합니다 <br>
<br>


@Setting, @Mapping 을 통해서 인덱스를 생성할 때 원하는 세팅 값(실제로 인덱스 생성할 떄 설정하는 값 처럼 JSON 파일이어야 합니다), 
원하는 매핑 값(실제로 인덱스의 매핑을 설정할 때 하는 값처럼 JSON 파일이어야 합니다)을 넣고 생성하는 것이 가능합니다 <br>

추가로 jpa 에서 @Column 처럼 @Field 을 통해서 mapping 시 원하는 elasticsearch의 저장 객체 타입을 지정해두고 
@Document 이 달려있는 객체에서 @Field 을 기반으로 동적으로 인덱스를 생성하도록 구성할 수 있습니다 <br>



+하나의 객체에서 JPA 의 entity 설정과 elasticsearch 의 document 선언을 함께 사용하고 싶다면 @Entity 와 함께 사용하는 것도 가능은 하지만 <br>
만약에 그렇게 하게 된다면 중복으로 사용하고 싶은 객체에 대해서 각각 jpaRepository, elasticsearchRepository을 구분해서 선언해줘야 합니다 <br>
또한 처음에 설정했던 configuration에서의 @EnableElasticsearchRepositories 에다가 elasticsearchRepository 을 상속받는 인터페이스들을 명시해주는게 필요합니다 <br>
이렇게 elasticsearch repository 설정을 정리해주고 나서는 jpa repository 를 auto scan 해주는 부분을 추가로 설정이 필요합니다 <br>
mainApplication 에서 @EnableJpaRepositroy 을 선언하고 해당 jpaRepository 을 상속받는 인터페이스들을 다 명시해주어야 합니다 <br>

따라서 만약에 하나의 객체를 디비에도 적재하고 싶고 ES 에도 적재하고 싶다면 >> 별도의 데이터 베이스이기 때문에 데이터의 관리 자체도 따로 분리해서 관리하는 것이 좋다고 생각합니다 <br>
<br><br>


## elasticsearchRepoistory 사용
기존에 jpaRepoistory 사용하는 것과 동일하다고 생각하면 좋습니다 <br>
단순히 상속받는 인터페이스가 JpaRepoistory > ElasticsearchRepoistory 로 변경되었다고 보면 됩니다 <br>

<br>
사용도 동일하게 사용함 >> save, saveAll, findBy~ 등등 jpa 에서 사용하던 것과 동일하게 사용합니다 <br>

지정한 데이터의 등록, 수정, 삭제, 조회를 지정해주는 메소드를 통해서 사용하는 것이 가능합니다 <br>
조회 같은 경우에는 3가지의 방법을 기반으로 해서 사용하는 것이 가능합니다 <br>

조회1 - repository 에서 기본 제공해주는 메소드를 통해서 <br>

<br>
조회2 - @Query 을 사용해서 커스텀하게 원하는 쿼리를 통해서 <br>
native query 와 같은 경우에는 jpa에서는 실제 SQL 문을 넣었다면 ElasticsearchRepository 에서는 api 로 작성하던 쿼리를 그대로 넣어주면 됩니다

+org.springframework.data.elasticsearch.annotations.Query 를 import 받음
<br>

<br>
조회3 - queryDsl
<br>

QueryDsl 사용 <br>
https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html

<br>
기존에 jpa에서 사용했던 것과 유사합니다 > 단순하게 sql 문들이 메소드로 나와있는 것 처럼 elasticsearch 의 조회 쿼리문의 메소드로 변경되어 있다는 점 정도가 차이 입니다 <br>

<br><br>



## 테스트코드 작성 

DB를 예시로 생각해보면 테스트 시 DB를 사용하는 코드를 테스트를 방법은 크게 3가지입니다 > realDB, in-memory, embedded <br>
직접 데이터에 붙는 방식 같은 건 테스트코드와 디비 환경이 너무 종속적이라고 생각합니다 <br>
테스트를 하면서 생기는 데이터에 대한 관리가 필요해지고 <br>
디비 환경의 상태에 따라서 테스트코드가 영향을 받게 될 것이라고 생각합니다 <br> 
하여 직접 붙는 방법이 아니라 다른 방법으로 선택하고 싶었습니다 <br>

<br>
테스트 방법을 찾다가 공식 문서를 확인해보니 elasticsearch로 테스트하기 위한 방법을 개발자가 가이드가 있었습니다 <br>
(테스트 방법에 대한 가이드가 삭제되었다는 의미 > 기본적인 테스트 방법은 #55258 이슈 대화 내용에서 가이드가 있다는 의미)

![img_6.png](img_6.png) <br>
https://www.elastic.co/guide/en/elasticsearch/reference/7.15/integration-tests.html
<br><br>

#55258번호 이슈 <br>
![img_7.png](img_7.png) <br>
https://discuss.elastic.co/t/in-memory-testing-with-resthighlevelclient/106196/5

위의 elasticsearch 개발자의 대답은 간단합니다 <br>
embedded 테스트 기능을 추후 릴리즈에 추가해줄 수 있느냐 > 그럴일 없다, embededd 환경은 제공하지 않을 것이니 만약 통합 테스트가 필요하다면 이런 테스트 방법을 사용해봐라 <br>
그러고 추천해주는 방법이 바로 Docker and TestContainer 입니다 <br> 

<br>


TestContainer? > docker 를 기반으로 junit 테스트를 할 떄 도와주는 라이브러리입니다 <br>
- java로 container 등록 가능
- dockerFile, docker-compose, docker-hub을 통해서 컨테이너 동작 가능
- 동작시킨 컨테이너 생명주기 관리가능
- parallel 테스트 가능
- 다양한 모듈들이 존재해서 사용성이 높음
- 단점으로는 테스트 코드의 시간이 좀 오래걸림
- 또 단점으로는 컨테이너를 테스트코드가 끝날 때 중지할 수 밖에 없어서 >> 컨테이너가 자꾸 쌓이니 싱글톤으로 가져갈 수 있도록 고려 필요 


https://java.testcontainers.org/modules/elasticsearch/ <br>
공식 홈페이지를 보면 제공해주는게 elasticsearch 도커 이미지를 통해서 직접 elasticsearch 컨테이너 을 띄워서 사용하는 방법입니다 <br>
코드단에서 도커 이미지를 가져와서 > 컨테이너를 실행시키고 > 테스트  <br>

==== 위의 방법으로 바로 띄우면 nori analyzer 의 적용이 불가능 === <br>
해당 건을 해결하기 위해서

testcontainer 에서 제공해주는 ElasticsearchContainer 가 아닌 그냥 GenericContainer 을 통해서 Elasicsearch 을 띄우고 + runWith 을 통해서 nori 형태분석소를 등록해주어야 합니다 <br>

요건.. 결국 개발자 피씨에 도커가 있어야 하고.. 인터넷망이 존재해야한다는 문제가 있습니다 <br>
인터넷망에서는 손쉽게 테스트 되었고 아주 간단하게 사용할 수 있습니다 > 오프라인 환경에서의 사용법은 못찾았습니다 <br>
추후에 인터넷망에서 도커만 설치되어있다면 사용하기 아주 좋은 툴인 것으로 보입니다 다양한 모듈들이 존재하니 원하는대로 사용할 수 있습니다 <br>
<br>

test container 가 실패하고 그 다음으로는 custom 하게 공식으로 지원하는 방법은 아니지만 개발자들이 직접 만든 embedded elasticsearch 으로 사용해보고자 했습니다 <br> 
구글링해보니 해당 팀에서 만든 라이브러리가 있었고 이걸 사용해봤습니다 <br>
https://github.com/allegro/embedded-elasticsearch

보면 해당 라이브러리는 우선 사용하지 않는 것을 공식적으로 추천하고 있습니다 <br>
elasticsearch 공식에서도 embedded 방식은 사용하지 않도록 이야기 했기 때문에 커스텀에서도 함께 경고해주는 것으로 보입니다 <br> 
현대 사용하고 있는 elasticsearch 버전과 테스트에서 사용할 버전이 다르다는 점이 있긴 하지만 우선 elasticsearch 의 버전 때문에 생기는 문제를 찾지는 못했기 떄문에 해당 방법으로
테스트를 진행하면 좋을 것이라고 생각합니다 <br>
<br><br>


<br><br><br><br><br><br><br><br><br><br>





