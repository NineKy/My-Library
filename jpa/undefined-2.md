# 엔티티 매핑

JPA를 사용하는 이유는 엔티티(개체)와 테이블을 정확하게 **매핑해서** 객체 지향적으로 데이터 베이스에 접근하기 위해서이다.

여기서 엔티티란? - 우리가 알고 있는 그 실제, 객체라고 생각하면 될 듯 하다

### 매핑하는데 필요한 (앞으로 알아볼) 애노테이션

* 객체와 테이블의 매핑 : @Entity, @Table
* 기본 키(primary key) 매핑 : @Id
* 필드와 컬럼 매핑 : @Column, @Transient
* 연관관계 매핑 : @OneToMany, @JoinColumn

### @Entity

JPA를 사용해서 객체와 테이블을 연동하기 위해서 **가장 필수적으로** 들어가야하는 애노테이션으로, 이것을 붙히면 자동으로 JPA에서 관리해준다.

기능 : JPA에서 사용할 엔티티 이름을 지정한다. 보통 기본값인 클래스 이름을 사용되어서 매핑되기 때문에 테이블명과 엔티티의 명을 잘 생각해서 처리해줘야 한다. \*\*\*\*

주의사항

* 기본 생성자가 필수적으로 필요 → 파라미터가 없는 public, protected 생성자(@NoArgsConstructor)
  * 자바는 생성자가 없으면 자동으로 빈 생성자를 만들어주는데, 만약 따로 만들어진 생성자가 있다면 그냥 텅 빈 생성자를 만들어서 위의 조건에 맞춰주자!
* final 클래스, enum, interface, inner 클래스에는 사용할 수 없다
* 저장할 필드에 final은 사용할 수 없다

### @Table

@Table은 엔티티와 매핑할 테이블을 지정하는 애노테이션이다. 옵션으로 만약 name = “이름값” 이렇게 해두면 매핑하고 싶은 엔티티 이름은 사용해서 매핑해주는데, 만약에 그냥 이 애노테이션만 붙힌다면 해당 class의 이름을 테이블의 이름으로 매핑해준다.

속성

* name : 매핑할 테이블 이름
* catalog : catalog 기능이 있는 데이터베이스에서 catalog을 매핑한다.
  * 데이터 베이스에서 catalog란 데이터 베이스에 있는 개체들에 대한 정의를 가지고 있는 메타 데이터들로 구성된 데이터 베이스 내의 인스턴스이다
* schema : schema 기능이 있는 데이터 베이스에서 schema를 매핑한다.
* uniqueConstraints(DDL) : DDL 생성 시에 유니크 제약조건을 만든다. 2개 이상의 복합 유니크 제약조건도 만들 수 있다. 참고로 이 기능은 스키마의 자동 생성 기능을 사용해서 DDL을 만들 때만 사용하고 이건 스키마를 자동으로 생성해주는 기능을 사용했을때만 적용되는 속성이다. 만약 직접 DDL을 만든다면 필요 없는 속성이겠다
  * Data Definition Language(데이터 정의어) : 테이블과 같은 데이터 구조를 정의하는데 사용되는 명령어
    * CREATE, ALTER, DROP, RENAME, TRUNCATE

예제에 추가적으로 컬럼 타입들을 추가

* @Enumerated : enum 타입을 컬럼으로 사용할 수 있음
  * 2가지 타입이 있음
    * EnumType.ORDINAL - enum의 순서 값을 저장, 이건 좀 그렇게 좋은 방법은 아닌 듯 함
    * EnumType.STRING - enum의 이름을 저장
* @Temporal : 날짜 타입을 매핑해주는데, LocalDate이나 LocalDateTime을 사용한다면 생략할 수 있음
* @Lob : 데이터 베이스에서 varchar보다 긴 데이터를 저장할 때 사용하고 매핑하는 필드 타입이 만약 문자라면 CLOB를 매핑해주고 나머지는 BLOB로 매핑
  * CLOB : String, char\[]
  * BLOLB : byte\[]

JPA에서 제공하는 데이터 베이스에서 스키마를 자동으로 생성하는 기능 추가하기

application.properties에

```yaml
spring.jpa.hibernate.ddl-auto=create
```

이렇게 속성을 추가해주면, 애플리케이션 실행 시점에 데이터 베이스 테이블을 자동으로 생성한다.

이것을 보기 위해서는

```yaml
spring.jpa.hibernate.show_sql=true
```

를 통해서 애플리케이션 실행 시, jpa에 의해 자동으로 생성되는 SQL을 확인할 수 있다.

ddl-auto 속성에는 몇 가지가 있음

* create : 기존 테이블을 삭제하고 새로 생성(drop → create)
* create-drop : create 속성에 추가로 애플리케이션을 종료할 떄 생성한 DDL을 제거한다(drop → create → drop)
* update : 데이터 베이스 테이블과 엔티티 매핑 점보를 비교해서 변경 사항만 수정
* validate : 데이터 베이스 테이블과 엔티티 매핑 정보를 비교해서 차이가 있으면 경고를 남기고 애플리케이션을 실행하지 않는다. 이 설정을 해두면 DDL을 수정하지 않는다
* none : 자동 생성 기능을 사용하지 않으려면 hibernate.hbm2ddl.auto의 속성 자체를 삭제하거나 유효하지 않은 값은 옵션 값을 주면 된다.
* 속성을 보면 예상할 수 있겠지만 개발 단계별로 중요한 속성 값들을 골라서 사용하면 된다.
  * 개발 단계에서는 create, update
  * 자동화 테스트 단계, CI서버에서는 create, created-drop
  * 테스트 서버에서는 update, validate
  * 스테이징과 운영에서는 none, validate

이름 매핑의 방식

자바에서 객체를 표시하는 방법은 Camel 케이스를 사용한다. camel케이스란 맨 앞에는 소문자로 시작해서 다른 단어로 바뀔 때 대문자로 바꾼다. roleType처럼

하지만 데이터 베이스에는 언더바를 사용해서 구분한다. role\_type처럼

따라서 이렇게 구성했었다

```yaml
@Column(name="role_type")
String roleType;
```

하지만 이렇게 매핑하는 것을 맞춰서 진행할수도 있지만 원하는 매핑 전략으로 수정할 수 있다. 물론 본인은 저 방식에 적응되어 있기 때문에 굳이 이렇게 할... 크흠 필요하면 hibernate improvednamingstrategy 으로 검색

### @Id

해당 엔티티에 primary key(기본키)를 설정해주는 애노테이션

데이터 베이스에서 제공해주는 기본키 생성 전략에는 여러가지 있다.

* 직접 할당 : 기본 키를 애플리케이션에서 직접 할당
* 자동 생성 : 대리키 사용 방식
  * IDENTITY : 기본 키 생성을 데이터 베이스에 위임한다
  * SEQUENCE : 데이터 베이스의 시퀀스를 사용해서 기본 키를 할당
  * TABLE : 키 생성 테이블을 사용

기본 키 직접 할당

자바에서 @Id에 적용할 수 있는 타입들

자바 기본형, 자바 wrapper형, String, java.util.Date, java.sql.Date, java.math.BigDecimal, java.math.BigInteger

기본 키 자동 생성 - IDENTITY

기본 키의 생성을 데이터 베이스에게 위임한다는 의미이다.

예를 들어 insert할 당시에, 기본 키에 해당 하는 값이 안들어오면 데이터 베이스에서 알아서 값을 생성해서 삽입된다. 물론 이 부분에서 @GenerationValue 애노테이션을 사용해서 strategy 속성 값을 GenerationType.IDENTITY를 사용해서 설정을 해주어야 한다.

내부 동작은 먼저 엔티티를 데이터 베이스에 저장한 후에 식별자를 조회해서 엔티티의 식별자에 할당하는 방식

**이걸 사용하면 JPA는 기본 키 값을 가져오기 위해서 데이터 베이스를 추가로 조회한다는 점!!!!!!**

기본 키 자동 생성 - SEQUENCE

데이터 베이스 시퀀스는 유일한 값을 순서대로 생성하는 특별한 데이터 베이스 오브젝트이고 이것을 사용해서 기본 키를 생성하는 전략이다.

만약에 이걸 사용하기 위해서는 속성을 GenerationType.SEQUENCE로 처리해주고, generator를 따로 선언해서 설정을 해줘야한다.

이것의 내부 동작은 persist()를 호출할 때 먼저 데이터 베이스 시퀀스를 사용해서 식별자를 조회하고 조회한 식별자를 엔티티에 할당한 후에, 해당 엔티티를 영속성 컨텍스트에 저장한다.

@SequenceGenerator의 속성

* name : 식별자 생성기 이름(필수값)
* sequenceName : 데이터 베이스에 등록되어 있는 시퀀스 이름
* initalValue : DDL 생성 시에만 사용되고, 시퀀스 DDL을 생성할 때 처음 시작하는 수를 지정(기본값 1)
* allocationSize : 시퀀스 한 번 호출에 증가하는 수이고 성능 최적화에 수치 조절(기본값 50)
  * 데이터 베이스 시퀀스 값이 하나씩 증가하도록 설정되어 있으면 반드시 1로 설정해야함
* catalog, schema : 데이터 베이스 catalog, schema 이름

코드정의

```java
public class DatePrefixedSequenceIdGenerator extends SequenceStyleGenerator {

    public static final String DATE_FORMAT_PARAMETER = "dateFormat";
    public static final String DATE_FORMAT_DEFAULT = LocalDate.now().format(DateTimeFormatter.ofPattern("yyyyMMdd"));
    private String datePrefix;

    public static final String NUMBER_FORMAT_PARAMETER = "numberFormat";
    public static final String NUMBER_FORMAT_DEFAULT = "%07d";
    private String numberFormat;

    public static final String TIME_FORMAT_PARAMETER = "timeFormat";
    public String TIME_FORMAT_DEFAULT = LocalDateTime.now().format(DateTimeFormatter.ofPattern("HH:mm:ss"));
    private String timeFormat;

    private UUID uid;
    private String format;

    @Override
    public Serializable generate(SharedSessionContractImplementor session,
                                 Object object) throws HibernateException {
        return
                //prefix된 값을 넣어주는 방식
//                String.format(datePrefix, LocalDate.now())
//                +"_"+
//                String.format(timeFormat, super.generate(session, object))
                LocalDate.now().format(DateTimeFormatter.ofPattern("yyyyMMdd"))
                        + "_" +
                        LocalDateTime.now().format(DateTimeFormatter.ofPattern("HH:mm:ss"))
                        + "_" +
                        String.format(numberFormat, super.generate(session, object));
    }

    @Override
    public void configure(Type type, Properties params,
                          ServiceRegistry serviceRegistry) throws MappingException {
        super.configure(LongType.INSTANCE, params, serviceRegistry);

        datePrefix = ConfigurationHelper.getString(DATE_FORMAT_PARAMETER,
                params, DATE_FORMAT_DEFAULT);
        numberFormat = ConfigurationHelper.getString(NUMBER_FORMAT_PARAMETER,
                params, NUMBER_FORMAT_DEFAULT);
        timeFormat = ConfigurationHelper.getString(TIME_FORMAT_PARAMETER,
                params, TIME_FORMAT_DEFAULT);

    }
}
```

사용

```java
@Data
@Entity
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class TestHistory {
    @Id
    @GeneratedValue(strategy= GenerationType.SEQUENCE, generator = "history_date_seq")
    @GenericGenerator(name="history_date_seq", strategy = "com.kona.tester.DatePrefixedSequenceIdGenerator")
    private String id;
    private String agencyName;
    private String testName;
    private String status;
    private String errMsg = "X";
}
```

테이블 전략 - 키 생성 전용 테이블을 만들고 여기에 이름과 값으로 사용할 컬럼을 만들어 데이터 베이스 시퀀스를 흉내내는 전략

@TableGenerator를 사용해서 테이블 키 생성기를 등록

속성

* name : 식별자 생성기 이름 (필수)
* table : 키 생성 테이블 명
* pkColumnName : 시퀀스 컬럼명
* valueColumnName : 시퀀스 값 컬럼명
* pkColumnValue : 키로 사용할 값 이름
* initialValue : 초기 값, 마지막으로 생성된 값이 기준
* allocationSize : 시퀀스 한 번 호출에 증가하는 수이고 성능 최적화에 수치 조절(기본값 50)
* catalog, schema : 데이터 베이스 catalog, schema 이름
* uniqueConstraints(DDL) : 유니크 제약 조건을 지정할 수 있다.

이 전략은 값을 조회하면서 select하고, 값 증가를 위해 update를하기 때문에 select를 2번이나 하는 단점이 있다.

### 식별자 선택 전략

기본 키는 이러한 조건들을 만족하는 속성을 가지고 있어야한다

1. null값은 허용하지 않는다.
2. 유일해야한다.
3. 변해선 안된다.
