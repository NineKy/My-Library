# ElasticSearch 기능
Elasticsearch 3번째 세미나 내용 <br>
세 번째 세미나 내용은 ES가 데이터를 저장하고 검색하는 과정, ES에서 제공해주는 기능에 대해서 작성해보고자 합니다 <br>
<br>

#### 이전 세미나에서의 질문 사항
노드가 마스터가 될 수 있는 노드로 설정하는 값은 노드를 설정하는데 있어서 node.master: true 라는 설정을 통해서 업그레이드가 가능한 노드에 대한 설정을 하는 것이 가능합니다 <br> 
샤드간에서는 레플리카에서 프라이머리로 넘어갈 수 있도록 설정하는 방법은 따로 존재하지 않습니다 <br>
망가진 프라이머리를 대신하기 위해서 레플리카가 자동으로 프라이머리로 승격되는 방식은 ES에 내장되어 있는 복구 메커니즘 중 일부라고 볼 수 있습니다 <br>


### 색인 및 검색 과정

ES에서 색인이라는 단어는 데이터를 적재하는 과정을 의미합니다\
정확하게는 인덱스를 생성하고 매핑을 검증하고 역인덱스를 생성하고 document을 저장하는 과정입니다\
데이터를 저장하는 과정에서 샤드의 갯수를 설정하는 것이 중요합니다\
샤드의 갯수를 지정하는 과정은 처음부터 완벽한 샤드의 설정을 하는 것은 어려우니 설계 시 예측하고, 유스케이스에 맞춰서 차차 맞춰가는 것이 중요합니다\
\


색인의 성능을 고려할 때에는 가능한 많은 데이터 노드들이 색인 과정에 참여할 수 있도록 현재의 구조가 클러스터 구조로써 잘 동작하는지에 대해서 중점적으로 보면 좋습니다\
데이터 색인, 즉 데이터를 적재하게 되면 text 필드별로 역 인덱스를 생성합니다\
여기서 역 인덱스는 각 단어별로 어떤 document에서 해당 단어를 쓰는지 리스팅을 해두고 해당 구조로 저장하게되는 구조입니다\
역 인덱스를 설명할 떄 많이들 보여주는 예시는 책에서의 차례 vs 찾아보기(책맨뒤에있는) 를 들어서 보여주곤 합니다\
![img\_3.png](img\_3.png)

\
키워드 방식이기 때문에 키워드를 찾기 위해서 사용하는 like 보다는 더 좋은 성능을 보여줍니다\
추가로 대신 역인덱스 방식을 사용하고 있기 때문에 데이터를 색인 속도 자체는 같은 양의 데이터를 기준으로 RDBMS 에서 insert 하는 것보다는 더 걸립니다\
\> 하지만 위에서의 the 나 a 와 같이 너-무 많이 사용되는 단어는 그렇게 큰 성능적 이점을 찾지 못할 수 있습니다\
\


문자를 저장할 떄는 문자열을 토크나이징을 통해서 저장하는데 해당 방식은 캐릭터필터 > 토크나이저 > 토큰필터 순으로 진행됩니다\


![img\_4.png](img\_4.png)\
character filter 는 전체 문장에서 특정 문자를 대체하거나 제거 하는 역할을 진행합니다\
character filter 의 종류

* pattern replace : java 정규식을 통해서 변환
* mapping : 문자열을 다른 문자열로 변환(변환하고 싶은 문자열 입력 필요)
* HTML strip : html 문자를 제거

\


tokenizer 는 설정한 토크나이저를 베이스로(하나만 지정가능) N개의 단어로 분리합니다\
tokenizer의 종류

* whitespace : 공백문자를 기반으로 단어를 추출
* standard : 공백, 마침표, 물음표 등을 기반으로 추출
* classic : 영어에 특화되었으며 이메일이나 전화번호 인식 가능

\


token filter 는 토크나이저를 통해서 분리된 단어를 필터로 지정한 규칙에 따라서 처리합니다\


다양하게 있으니 필요한 것을 사용하면 좋습니다\
https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-tokenfilters.html\
예시 중 하나로 stop token filter 이란게 있는데 해당 필터는 a, an, the, are 등 이러한 필요 없는 단어들을 제거하는 방식입니다\
\
\


실제로 이제 데이터가 역색인되어 들어가는 과정입니다\
![img\_2.png](img\_2.png)\


색인 과정은 프라이머리 샤드에서만 가능했던 것과 달리, 검색 과정은 프라이머리, 복제 샤드 모두에서 가능합니다\
검색하는 단계는 검색어 분석 > 역인덱스 검색 > 결과표시 단계로 이루어진다고 볼 수 있습니다\
검색 과정의 성능같은 경우에는 복제 샤드에서도 가능하다고 위에서 언급했었는데, 복제샤드의 수 자체는 인덱스에서 동적으로 설정하는 것이 가능하니 성능이 안나온다 싶으면 복제 샤드의 수를 조정하는 것으로 조절이 가능합니다\
추가로 가능한 모든 데이터 노드가 검색 과정에 참여하고 있는지 모니터링해주는 것이 중요합니다\
\
\
\