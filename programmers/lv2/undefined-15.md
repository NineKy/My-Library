# 기능개발

[https://programmers.co.kr/learn/courses/30/lessons/42586](https://programmers.co.kr/learn/courses/30/lessons/42586)

기존에 원시적으로 풀었던 방법이 있더라

```java
import java.util.*;
class Solution {
    public int[] solution(int[] progresses, int[] speeds) {
        int[] answer = {};
        
        ArrayList<Integer> prog = new ArrayList();
        ArrayList<Integer> time = new ArrayList();
        
        for(int i=0; i<progresses.length; i++){
            prog.add(progresses[i]);
            time.add(speeds[i]);
        }
        
        ArrayList<Integer> result = new ArrayList();
        
        while(prog.size()>0){
            for(int i=0; i<prog.size(); i++){
                prog.set(i, prog.get(i)+time.get(i));
            }
            
            int cnt = 0;
            for(int i=0; i<prog.size(); i++){
                if(i==0 && prog.get(i) >= 100){
                    cnt++;
                    prog.remove(i);
                    time.remove(i);
                    i--;
                }else if(cnt>0 && prog.get(i) < 100){
                    break;
                }else if(cnt>0 && prog.get(i) >= 100){
                    cnt++;
                    prog.remove(i);
                    time.remove(i);
                    i--;
                }
            }
            if(cnt>0){
                result.add(cnt);
            }
        }
        
        answer = new int[result.size()];
        
        for(int i=0; i<result.size(); i++){
            answer[i] = result.get(i);
        }
        
        return answer;
    }
}
```

문제 자체가 스택/큐 문제이니까 스택을 활용해서 풀어보는걸로하자..!

```java
import java.util.*;
class Solution {
    public int[] solution(int[] progresses, int[] speeds) {
        int[] answer = {};

        Stack<Integer> stack = new Stack<>();
        for(int i=progresses.length-1; i>=0; i--){
            stack.push((100-progresses[i])/speeds[i] + ((100-progresses[i])%speeds[i]>0 ? 1 : 0));
        }

        ArrayList<Integer> list = new ArrayList<>();
        while(!stack.isEmpty()){
            int cnt = 0;
            int n = stack.pop();
            cnt++;
            while(!stack.isEmpty() && n >= stack.peek()){
                cnt++;
                stack.pop();
            }
            list.add(cnt);
        }

        answer = new int[list.size()];
        for(int i=0; i<answer.length; i++){
            answer[i] = list.get(i);
        }

        return answer;
    }
}
```
