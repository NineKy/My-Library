# JUnit5
## Junit의 구성?
Jupiter + Vintage + JUnit Platform <br>
Jupiter : JUnit5을 사용하기 위해서 제공해주는 새로운 프로그래밍 모델이고 이놈 밑에 있는애들은 JUnitPlatform에서 Jupiter 기반 테스트를 실행하기 위한 TestEngine을 제공 <br>
Vintage : 기존의 JUnit3, JUnit4가 존재한다면 이놈들을 돌아가게해주는 TestEngine을 제공 <br>
JUnit Platform : JVM에서 테스트 프레임워크를 돌릴 수 있도록 도와주는 놈이고, 테스트 코드를 짜기 위해서 필요한 API들을 정의해주고 IDE에도 들어가 있기도 함 <br>
<br><br>

## 단순한 문법과 테스트 코드의 구성 실습
문법을 사용해보기 위해서 단순하게 함수들, 애노테이션들을 사용해보자 <br>
근데 대충 이름만봐도 감이 오는 것들이라서 다행이다 <br>

- @BeforeAll
- @AfterAll
- @BeforeEach
- @AfterEach
- @Test
- @DisplayNameGeneration
- @DisplayName
- @Disabled
- @Tag
- CustomTag
- @RepeatedTest
- @ParameterizedTest
  - @ValueSource
  - @NullAndEmptySource
- assertNotNull
- assertNotEquals
- assertAll
- assertThrow
- assertTimeout
- assumeTrue
- assumingThat


<br><br><br>

<br><br><br><br><br><br><br><br><br><br>