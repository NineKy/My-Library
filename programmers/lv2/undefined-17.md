# 스킬트리

[https://programmers.co.kr/learn/courses/30/lessons/49993](https://programmers.co.kr/learn/courses/30/lessons/49993)

이것도 막혔다.. 2차원 배열로 index을 값으로 가지는 int2차원배열로 막 sorting 해서 비교해보려고 했지만..? default가 0이여서 다른 음수값을 두려고 하니까 막히는 케이스가 나와서 문제가 생겼다

그래서 구글링을 살짝해봤는데 이방법이 가장 신기했다

```java
import java.util.*;
class Solution {
    public int solution(String skill, String[] skill_trees) {
        int answer = 0;
        
        for(String st : skill_trees){
            String tmp = st;
            for(int i=0; i<st.length(); i++){
                String remov = String.valueOf(st.charAt(i));
                if(!skill.contains(remov)){
                    tmp = tmp.replace(remov, "");
                }
            }
            if(skill.indexOf(tmp) == 0)
                   answer++;
        }
        
        return answer;
    }
}
```
