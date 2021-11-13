# 없는 숫자 더하기

[https://programmers.co.kr/learn/courses/30/lessons/86051?language=java](https://programmers.co.kr/learn/courses/30/lessons/86051?language=java)

0부터 9까지의 숫자 중 일부가 들어있는 배열 `numbers`가 매개변수로 주어집니다. `numbers`에서 찾을 수 없는 0부터 9까지의 숫자를 모두 찾아 더한 수를 return 하도록 solution 함수를 완성해주세요.

### 제한사항

* 1 ≤ `numbers`의 길이 ≤ 9
* 0 ≤ `numbers`의 모든 수 ≤ 9
* `numbers`의 모든 수는 서로 다릅니다.

### 입출력 예

\[1,2,3,4,5,6,7,8,9,0] ⇒ 14

\[5,8,4,0,6,7,9] ⇒ 6

### 입출력 예 설명

**입출력 예 #1**

* 5, 9가 `numbers`에 없으므로, 5 + 9 = 14를 return 해야 합니다.

**입출력 예 #2**

* 1, 2, 3이 `numbers`에 없으므로, 1 + 2 + 3 = 6을 return 해야 합니다.

```java
import java.util.*;
class Solution {
    public int solution(int[] numbers) {
        int answer = 0;
        int[] sample = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
        boolean[] visited = new boolean[10];
        for(int i=0; i<numbers.length; i++){
                visited[numbers[i]] = true;
        }
        
        for(int i=0; i<10; i++){
            if(!visited[i])
                answer+=sample[i];
        }
        return answer;
    }
}
```
