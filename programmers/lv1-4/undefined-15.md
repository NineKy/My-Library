# 문자열 내 마음대로 정렬하기

[https://programmers.co.kr/learn/courses/30/lessons/12915](https://programmers.co.kr/learn/courses/30/lessons/12915)

허허.. 이걸 못풀다니....

문제가 뭐였냐면 정렬을 하는데, 같을 시, 기존의 문자열가지고 비교를 해야하는데 이걸 처리하는 방법을 몰랐다.....

결과적으로는 문자열을 비교하려는 캐릭터타입의 뒤에 붙혀서 전체를 비교하는 방법이였다

```java
import java.util.*;

class Solution {
    public String[] solution(String[] strings, int n) {
        String[] answer = new String[strings.length];
        
        String[] tmp = new String[strings.length];
        for(int i=0; i<strings.length; i++){
            tmp[i] = strings[i].charAt(n)+strings[i];
        }
        
        Arrays.sort(tmp);
        for(int i=0; i<strings.length; i++){
            answer[i] = tmp[i].substring(1, tmp[i].length());
        }
        
        return answer;
    }
}
```
