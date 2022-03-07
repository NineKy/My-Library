# 정렬(Sorting)

자주 나오기도하고, 필수적인 문제!

Java

Collection의 종류인 하나 ArrayList을 사용해봄

ArrayList는 정해진 크기가 없고 객체들이 추가되면서 저장 용량을 초과하게되면 자동으로 부족한 크기만큼 늘어남!

```java
//그냥 선언
ArrayList<타입> arr = new ArrayList<>();

//값 추가
arr.add(1); arr.add(2); arr.add(null);//널값도 추가가능

//값 제거
arr.remove(1) //1번째 값 제거
arr.clear() //모두 제거

//크기 구하기
arr.size();

//특정 index값 구하기
arr.get(0); //0번째 값 가져오기

//특정 값 검색
arr.contains(1); //list안에 1이 있는지 true/false
arr.indexOf(1); //1이 있는 index값 반환
```

### Array.sort VS Collections.sort

```java
//여기 안에 있으니까 꼭 import잊지말장
import java.util.Arrays;
import java.util.Collections;
//Array.sort()사용 방법-------------------------------------
String[] arr = {"1","3","b","B"};

//이렇게 사용
Arrays.sort(arr);

//결과는 1, 3, B, b 
//오름차순으로 정렬되고, 숫자 > 대문자 > 소문자 > 한글 순서임

//만약 내림차순을 원한다?
Arrays.sort(arr, Collections.reverseOrder());

//---------------------------------------------------------
//Collections.sort() 사용 방법
ArrayList<String> list = new ArrayList<String>();
list.add("1");list.add("3");list.add("b");list.add("B");

//오름차순
Collections.sort(list);
//내림차순
Collections.reverse(list);

```

### Comparator로 비교하기

위처럼 정수기준으로 정렬하는 방법이 아닌 사용자가 원하는 기준으로 정렬할 떄 사용하는 방법

```java
Collections.sort(비교하는 리스트, new Comparator<비교타입>(){
    @Override
    public int compare(비교타입 변수1, 비교타입 변수2){
        비교를 해줌
        값을 연산해서 음수가 나오면 변수1이 작다고 판단!!
        값을 연산해서 양수가 나오면 변수2가 작다고 판단!!
    }
});
```

일단은 Collections.sort형태를 사용하고, 중요한건 사용자함수를 Comparator안에서 compare함수를 통해서 계산을 해준다


<br><br><br><br><br><br><br><br><br><br>