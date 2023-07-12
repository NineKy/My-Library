build.gradle 을 통해서 spring-data-elasticsearch 적용


elasticsearch 접속을 위한 configuration 설정
@EnableElasticsearchRepositories 을 통해서 해당 jpa repository 을 사용하는 것처럼 ElasticsearchRepository 상속을 통해서 유사하게 사용

RestHighLevelClient (ES와 통신하기 위해 사용하는 client 객체) 를 빈으로 등록해서 자동으로 해당 객체로 통신하도록 등록

해당 빈에서 통신과 관련해서 필요한 정보들을 세팅 가능(타임아웃시간, 인증서, id, pwd 등)





elasticsearch 에 저장하기 위한 인덱스 객체 선언
@Document(indexName = "저장하고자 하는 인덱스이름") 을 통해서 인덱스를 명시

@Setting, @Mapping 을 통해서 인덱스를 생성할 때 원하는 세팅 값(json파일로 지정해줘야함), 원하는 매핑 값(json파일로 지정해줘야함)을 넣고 생성하는 것이 가능

@Field 을 통해서 mapping 시 원하는 es의 저장객체 타입을 지정 가능



>> jpa 와 함께 사용하고 싶다면 @Entity 와 함께 사용하는 것도 가능은 하지만

만약에 그렇게 하게 된다면 해당 객체에 대해서 jpaRepository, elasticsearchRepository을 구분하고

config 에서 지정한 @EnableElasticsearchRepositroies 에다가 elasticsearchRepository 을 상속받는 인터페이스들을 다 지정해서 넣어주고

mainApplication 에서 @EnableJpaRepositroy 을 선언하고 해당 jpaRepository 을 상속받는 인터페이스들을 다 지정해서 넣어주어야함



따라서 만약에 하나의 객체를 디비에도 적재하고 싶고 ES 에도 적재하고 싶다면 >> 따로 분리해서 관리하는 것이 좋아보임





elasticsearchRepoistory 사용
기존에 jpaRepoistory 사용하는 것과 동일하게 생겼음

단순히 상속받는 인터페이스가 JpaRepoistory > ElasticsearchRepoistory 로 변경됨

사용도 동일하게 사용함 >> save, saveAll, findBy~ 등등 jpa 에서 사용하던 것과 동일하게 사용

native query 와 같은 경우에는 jpa에서는 실제 SQL 문을 넣었다면 ElasticsearchRepository 에서는 api 로 작성하던 쿼리를 그대로 넣어주면 됨

+org.springframework.data.elasticsearch.annotations.Query 를 import 받는거 참고



여기서 데이터의

등록,

수정,

삭제,

조회1 - repository 에서 기본 제공해주는 메소드를 통해서

조회2 - @Query 을 사용해서 커스텀하게 원하는 쿼리를 통해서

의 조작 방법에 대해서 확인

조회3 - 밑에서의 queryDsl





QueryDsl 사용
https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html

기존에 jpa에서 사용했을 때와 비슷 > 단순하게 sql 문들이 es의 쿼리문법으로 바뀐 정도?

필요한건 확인해보면서 사용하면 될 것으로 보임





테스트코드 작성
테스트 코드를 작성하는데 있어서 실제 기동 중인 ES에 붙는 것도 방법이긴 하지만 테스트 데이터를 항상 정리해야 하는 단점이 있음

h2 와 같은 embededd ES 환경이 필요했음

공식 문서에서 확인해보니 elasticsearch로 테스트하기 위한 방법을 개발자가 가이드 줌

https://www.elastic.co/guide/en/elasticsearch/reference/7.15/integration-tests.html

https://discuss.elastic.co/t/in-memory-testing-with-resthighlevelclient/106196/5

심규도(Kyudo Shim) > Elasticsearch 적용 > image2023-7-12_13-5-20.png



>> embededd 환경은 제공하지 않을 것이니 이런 테스트 방법을 사용해봐라

여기서 제공해준 방법 중에 테스트 컨테이너에 ES 이미지를 넣어서 테스트 시 컨테이너를 띄워서 테스트를 하는 방법을 선택



https://java.testcontainers.org/modules/elasticsearch/

위 페이지에서 제공해주는게 elasticsearch 도커 이미지를 통해서 직접 embedded elasticsearch 을 띄워서 사용하는 방법

코드단에서 도커 이미지를 가져와서 > 컨테이너를 실행시키고 > 테스트

==== 위의 방법으로 바로 띄우면 nori analyzer 의 적용이 불가능 ====



따라서 해당 건을 해결하기 위해서

testcontainer 에서 제공해주는 ElasticsearchContainer 가 아닌 그냥 GenericContainer 을 통해서 Elasicsearch 을 띄우고 + runWith 을 통해서 nori 형태분석소를 등록해주자











