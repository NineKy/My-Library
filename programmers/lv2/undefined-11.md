# 영어 끝말잇기

[https://programmers.co.kr/learn/courses/30/lessons/12981](https://programmers.co.kr/learn/courses/30/lessons/12981)

2단계 코테 부수기 진행중... 뭐 조금 헷갈려서 애먹긴했지만? 그렇게 엄청 고생하지는 않았던 문제\~

```java
import java.util.*;
class Solution {
    public int[] solution(int n, String[] words) {
        //첫 번째에는 총 단어들중에서 몇 번째에 탈락했는지, 두 번째에는 개인의 몇번째였는지
        int[] answer = {0,0};
        ArrayList<String> used = new ArrayList<>();
        int round = 1;
        int userNumber = 1;

        used.add(words[0]);

        for(int i=1; i<words.length; i++){
            String s1 = words[i-1];
            String s2 = words[i];
            userNumber++;
            if(!(s1.charAt(s1.length()-1) == s2.charAt(0))){
                answer[0] = userNumber;
                answer[1] = round;
                return answer;
            }
            if(used.contains(s2)){
                answer[0] = userNumber;
                answer[1] = round;
                return answer;
            }
            used.add(s2);
            if(userNumber == n){
                userNumber=0;
                round++;
            }
        }
        return answer;
    }
}
```
