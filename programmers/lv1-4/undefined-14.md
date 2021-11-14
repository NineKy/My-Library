# 문자열 내림차순으로 배치하기

[https://programmers.co.kr/learn/courses/30/lessons/12917](https://programmers.co.kr/learn/courses/30/lessons/12917)

### **문제 설명**

문자열 s에 나타나는 문자를 큰것부터 작은 순으로 정렬해 새로운 문자열을 리턴하는 함수, solution을 완성해주세요.s는 영문 대소문자로만 구성되어 있으며, 대문자는 소문자보다 작은 것으로 간주합니다.

### 제한 사항

* str은 길이 1 이상인 문자열입니다.

### 입력

```java
"Zbcdefg"
```

### 출력

```java
"gfedcbZ"
```

1단계 문제였고 함수를 배워가자는 생각으로 해봤다.

java의 util 패키지의 Array에 존재하는 Arrays.sort(배열)을 사용하게되면 배열이 자동으로 오름차순으로 정렬된다.

같은 패키지 내에 존재하는 Arrays.asList(배열)은 배열을 리스트형태로 만들어주는 역할을 한다.

java의 util 패키지의 Collection에 존재하는 Collections.reverse(리스트)는 순서를 반대로 만들어줌

```java
import java.util.*;
class Solution {
    public String solution(String s) {
        String answer = "";
        String[] arr = s.split("");
        Arrays.sort(arr);
        Collections.reverse(Arrays.asList(arr));
        for(int i=0; i<arr.length; i++){
            answer+=arr[i];
        }
        return answer;
    }
}
```
