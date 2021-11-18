# 정수 내림차순으로 배치하기

[https://programmers.co.kr/learn/courses/30/lessons/12933](https://programmers.co.kr/learn/courses/30/lessons/12933)

어렵지 않은 문제였지만? 이제 Arrays.sort을 사용하는 방법에 대해서 다시금 생각하게 해주었다. 그리고 String과 int의 변환에 대해서 복습하는 느낌으로 스무스하게\~

```java
import java.util.*;
class Solution {
    public long solution(long n) {
        long answer = 0;

        String t = String.valueOf(n);
        String[] st = t.split("");
        int[] arr = new int[t.length()];
        for(int i=0; i<t.length(); i++){
            arr[i] = Integer.parseInt(st[i]);
        }

        Arrays.sort(arr);
        t="";
        for(int i=arr.length-1; i>=0; i--){
            t+=String.valueOf(arr[i]);
        }
        answer = Long.parseLong(t);

        return answer;
    }
}
```
