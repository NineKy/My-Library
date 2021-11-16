# 자연수 뒤집어 배열로 만들기

[https://programmers.co.kr/learn/courses/30/lessons/12932](https://programmers.co.kr/learn/courses/30/lessons/12932)

말 그대로인 문제였다

어렵지 않게 해결!

```java
import java.util.*;
class Solution {
    public int[] solution(long n) {
        int[] answer = {};

        String[] strings = String.valueOf(n).split("");
        answer = new int[strings.length];
        List<String> list = Arrays.asList(strings);
        Collections.reverse(list);
        for(int i=0; i<list.size(); i++){
            answer[i] = Integer.parseInt(list.get(i));
        }

        return answer;
    }
}
```
