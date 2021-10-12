# SpringBoot HATEOAS

Hypermedia-Driven RESTful Web Service

HATEOAS는 maturity model\(성숙도\)에 대해서 lv3에 해당하고, 대부분은 다 lv2에 해당해서 진행되고 있다.

레벨2는 메소드에 어떤 것을 진행할 것인지 설정해주고, url에는 자원을 집어넣어주며, JSON방식을 활용하는 방식이다.

레벨3은 레벨2에다가 추가적인 링크를 넣어주는 방식이다. 응답에다가 JSON+HAL\(링크를 추가\)

#### Hypermedia As The Engine Of Application State\(HATEOAS\)

hypermedia = data + links\(현재 resource에서 수행할 수 있는 액션\)

연관된 정보들도 링크에 담아서 응답하는 방식

* 애플리케이션은 클라이언트에게 API에 대한 가이드를 해야한다는 원칙을 가지고 있다. ⇒ 이건 다음으로 어떠한 step이 가능한지를 표시함으로써 가능한다
* 최종목적 : 클라이언트와 서버를 분리시켜서 각각 따로 진화할 수 있게 한다

#### HAL\(Hypertext Application Language\) Model

JSON + Link\(relation + href\) + embedded resource

#### 장점

* URL을 마음껏 바꿔도 괜찮다,
* API에 처음 접근하는 사람에게 좋은 안내서가 될 수 있다.
* client - server간 의존성을 줄이고 서로 독립적으로 바꿈

사용하기 위해선 pom.xml에서

spring-boot-starter-hateoas 을 추가해주어야함

#### WebMvcLinkBuilder

원래는 Link link = new Linkt\("[http://~](http://~/)"\); 이렇게 만들어야하지만

WebMvcLinkBuilder을 사용하게되면 링크를 하드코딩해서 사용하지 않고 생성하는것이 가능하다.

```text
Link link = WebMvcLinkBuilder
	.linkTo(methodOn(WebController.class).getAllAlbums())
	.withSelfRel()
	.withRel("albums");
```

#### RepresentationModel

EntityModel\(single resource\) → PagedModel → CollectionModel\(multiple resource\) → RepresentationModel

Entity를 기반으로해서 representationalModel을 만들게되는데 똑같을수도 있고 일부만 들어가있을수도 있다.

Presentation Layer에는 DTO\(data, getter, setter, 생성자 정도만으로 구성되어있고, 비지니스 로직은 따로 추가하지 않는다\)

Bussiness Layer에는 Domain\(entity\)

#### Representational model assembler

entity를 representational model\(DTO\)로 변환해주는 함수이다.

RepresentationModelAssembler라는 인터페이스를 이미 제공중에 있다. 이것을 implements 해서 만든 RepresentationModelAssemblerSupport 클래스를 상속받아서 사용하면 된다!!!

상속받고

toModel\(\), toCollectionModel\(\) 을 override해서 사용

내부 구현

instantiateModel\(\)을 통해서 인스턴스를 만들어주고

set으로 entity에 있는 속성들을 넣어주고

링크를 추가하기 위해서 add\(linkTo\(methodOn\(\).getActorById\)\)\)을 넣어줌

Optional&lt;Type&gt; : 값이 있으면 값을 주는데, 만약 null값이면 exception이 발생하게 되는데 이것을 방지해줌

.map\(actorModelAssembler::toModel\).map\(ResponseEntity::ok\).orElse\(ResponseEntity.notFound\(\).build\(\)\);

