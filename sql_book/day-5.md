# Day 5

_테이블 구조 참조하기_

## DESC

DESC은 description의 약어이고<br>

```sql
DESC sample21;
```

이렇게 테이블과 함께 사용한다<br>
DESC 명령으로 테이블에 어떤 열이 정의되어 있는지 알 수 있다<br>
조회해보면 크게 Field, Type, Null, Key, Default, Extra가 있다<br>

- Field : 열이름
- Type : 자료형(최대길이)
- Null : null값 허용여부
- Key : 해당 필드가 key로 지정했는지
- Default : 그 열에 주어진 기본값(데이터 생략시 기본값이 들어감)

<br><br><br>

## 자료형

#### Integer

정수값을 저장할 수 있는 자료형 -> 소수점을 포함할 수 없음<br>
<br>

#### Char

열의 최대 길이를 지정해서 입력된 열의 길이와 상관없이 항상 고정됨 문자열의 크기가 저장이 된다<br>
그래서 '고정 길이 문자열'이라고도 부르고 최대 길이보다 작은 문자열이 들어오면 마너지는 공백 문자로 저장<br>
<br>

#### Varchar

이것도 char와 같이 문자열을 저장할 수 있는 저장형인데 char와 다른점은 <br>
'가변 길이 문자열'이라는 점이여서 입력받은 숫자만큼의 크기만 저장이 된다<br>
<br>

#### Date

날짜값을 저장할 수 있는 자료형 -> YYYYMMDD형태<br>
<br>

#### Time

시간을 저장할 수 있는 자료형 -> HHMMSS형태<br>
<br>
<br><br><br><br><br><br><br>

