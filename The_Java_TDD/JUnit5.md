# JUnit5
## Junit의 구성?

## 단순한 문법과 테스트 코드의 구성 실습

```java
@DisplayNameGeneration(DisplayNameGenerator.ReplaceUnderscores.class)
public class LinkTest {

    @Test
    @DisplayName("\uD83D\uDE31")
    void create_first_test() {
        Link link = new Link();
        assertNotNull(link);
    }

    @Test
    void create_second_test() {
        Link link = new Link();
        assertNotNull(link);
    }

    @Test
    @Disabled
        //WHY FAIL?
    void update() {
        Link link = new Link();
        Link link2 = new Link();
        link2.setName("TEST");
        assertNotEquals(link, link);
    }

    @Test
    void assertionTest() {
        Link link = new Link();
        link.setName("link1");
        link.setCategory(CATEGORY.JAVA.toString());

        Link link2 = new Link();
        link2.setName("link2");
        link2.setCategory(CATEGORY.JAVA.toString());

        assertAll(
                () -> assertNotNull(link),
                () -> assertEquals(link.getCategory(), link2.getCategory(), () -> "두 링크들은 " + link.getCategory() + "범주안에 있는 링크들이다"),

                () -> assertTrue(link.getUrl() == null)
        );

        IllegalArgumentException exception = assertThrows(IllegalArgumentException.class, () -> link.checkCoke("나는콜라가좋아"));
        assertEquals(exception.getMessage(), "콜라는 안돼요!");

        //요놈은 해당 executable이 어떤 시간 안에 실행되어야하는지를 판단하는 함수이다
        //특징으로는 만약 executable이 길어지면 그 길어지는 시간만큼 기다린다는 것이 단점이다 그래서 만약 기다리지 않고 에러뱉고 넘어가기 위해서는 assertTimeoutPreemptively()를 쓰자
        assertTimeout(Duration.ofSeconds(10), () -> link2.checkCoke("test"));
    }

    @BeforeAll
    //BeforeAll은 무조건 static 타입으로 들어가야 한다, 실제 테스트 메소드 이름은 뭘로하든 상관없음
    static void startWith() {
        System.out.println("테스트 파일의 시작을 의미");
    }

    @AfterAll
    //spock에서의 cleanup과 같은 역할
    static void endWith() {
        System.out.println("테스트 파일의 종료를 의미");
    }

    @BeforeEach
        //each들은 static 이면 안된다..!
    void beforeEach() {
        System.out.println("==============================================");
        System.out.println("각 테스트의 시작");
        System.out.println("==============================================");
    }

    @AfterEach
    void afterEach() {
        System.out.println("==============================================");
        System.out.println("각 테스트의 종료");
        System.out.println("==============================================");
    }
}
```