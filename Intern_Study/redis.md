# Redis을 이용한 캐시

### Reids란? 

Remote Dictionary Server의 약어인 Redis는 "키-값" 구조의 비정형 데이터를 저장하고 관리하기 위한 오픈 소스 기반의 비관계형 데이터베이스 관리 시스템

모든 데이터를 메모리로 불러와서 처리하는 메모리 기반 DBMS

Memcached와 같이 인메모리\(In-memory\) 솔루션으로 분리된다.

* In-memory Cache : 메모리 캐시를 적용해서 DB의 부하를 감소
* Memcached : 데이터를 캐싱처리하는 데 사용하면 좋은 캐시이지만 Redis처럼 데이터타입이 다양하지는 않다.

Redis와 비교해보면 Memcached는 캐시 솔루션이고 여기에 저장소 개념까지 추가된 것이 Redis 메모리가 사라져도 원본 데이터로 즉시 복구할 수 있는 경우에는 Memcached사용 메모리가 사라지면 서비스에 장애가 발생하는 경우에는 Redis 사용

싱글 스레드로 구성되어있기 때문에 하나에 여러개의 서버를 띄우는 것이 가능!



#### 다양한 데이터 모델 지원 String

* List
* Set
* Hash



#### 데이터 처리 명령어 

* set : 데이터 저장
* mset : 여러개의 데이터 저장
* get : 저장된 데이터 조회
* mget : 여러개의 데이터 조회
* rename : 저장된 데이터 변경
* keys \* : 저장된 모든 key를 검색
* exists : 검색 대상의 key 존재 여부 확인
* del 키값 : 해당 키-값 삭제
* flushall : 전부 다 삭제





### 실습 Redis 설치

spring-boot-starter-data-redis 의존성 추가

RedisTemplate&lt;String, Object&gt;는 get/set을 위한 객체이다.

캐시의 사용을 위해서 SpringBootApplication 애노테이션 밑에 EnableCaching 애노테이션을 설정해준다.



캐싱 사용 대상 선언의 방법으로는

* @Cacheable : 조회해서 없으면 캐시를 생성하고 결과를 반환, 만약 캐시된 값이 있는 경우에는 해당 값을 반환
* @CacheEvict : 캐시 제거
* @CachePut : 메소드의 실행을 방해하지 않고 캐시를 업데이트
* @Caching : 메소드에 적용할 여러 캐시 조작을 재 그룹화
* @CacheConfig : 클래스 수준에서 몇 가지 일반적인 캐시 관련 설정을 공유

key 정의 및 조건 정의 가능

: key 항목을 생략하게 되면 메소드명::파라미터값 으로 자동 등록된다. key에서 변수를 사용하고 싶다면 \#뒤에 붙여서 사용하면 되고, 문자값을 사용하고 싶다면 " " 내부 에 ' ' 을 사이에 집어넣어주면 된다 -&gt; 근데 왠만하면 식별이 가능한걸로 하자!







