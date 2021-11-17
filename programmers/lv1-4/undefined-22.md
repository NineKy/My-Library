# 실패율

[https://programmers.co.kr/learn/courses/30/lessons/42889](https://programmers.co.kr/learn/courses/30/lessons/42889)

오늘도 프로그래머스 1단계 카카오 문제 부수기 진행중...

이건 엄청나게 쉽게 풀지는 못했다...

막혔던부분은 정수형을 나눠서 실수형태로 넣어주는데 에러가 안나길래 잘 들어가나 싶었는데 알고보니 타입 형태변화를 안해서 안들어갔었다ㅠ

문제 자체는 어렵지 않게 해결!

```java
import java.util.*;
class Solution {
    public int[] solution(int N, int[] stages) {
        int[] answer = new int[N];
        
        double[] score = new double[N];
        double[] comp = new double[N];
        int total = stages.length;
        int fail = 0;
        for(int i=0; i<N; i++){
            
            for(int j=0; j<stages.length; j++){
                if(stages[j] == i+1){
                    fail++;
                }
            }
            score[i] = (double)fail/total;
            comp[i] = (double)fail/total;
            
            if(fail==0 && total==0){
                score[i] = 0;
                comp[i] = 0;
            }
            
            total-=fail;
            fail=0;
        }
        
        Arrays.sort(score);
        for(int i=score.length-1; i>=0; i--){
            for(int j=0; j<comp.length; j++){
                if(score[i] != -1 && score[i]==comp[j]){
                    answer[score.length-1-i] = j+1;
                    score[i] = -1;
                    comp[j] = -1;
                }
            }
        }
        
        return answer;
    }
}
```
