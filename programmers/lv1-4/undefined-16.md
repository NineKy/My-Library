# 약수의 개수와 덧셈

[https://programmers.co.kr/learn/courses/30/lessons/77884](https://programmers.co.kr/learn/courses/30/lessons/77884)

프로그래머스 레벨 1 부수기 진행중....

어렵지 않지요!

```java
import java.util.*;

class Solution {
    public int solution(int left, int right) {
        int answer = 0;
        int[] count = new int[right-left+1];
        int cnt=0;
        for(int i=left; i<=right; i++){
            ArrayList<Integer> list = new ArrayList<>();
            for(int j=1; j<=i; j++){
                if(i%j==0)
                    list.add(j);
            }
            count[cnt] = list.size();
            cnt++;
            list.clear();
        }

        for(int i=0; i<count.length; i++){
            if(count[i]%2==0)
                answer+=(left+i);
            else if(count[i]%2==1)
                answer-=(left+i);
        }
        return answer;
    }
}
```
