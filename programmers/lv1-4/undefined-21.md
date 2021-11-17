# 크레인 인형뽑기 게임

오랜만에 좀 막혔던 문제였다...

뭐일단 스스로 결국 풀긴했는데 좀 검색해보니까 스택을 사용한 경우가 많았다

처음에 문제 구현방식 고려할때 스택도 괜찮아보였는데 좋은 방법인거같으니 다음에 시간날때 스택을 사용해서 다시 풀어보자!

```java
import java.util.*;
class Solution {
    public int solution(int[][] board, int[] moves) {
        int answer = 0;

        int[][] resized  = new int[board.length][board.length];
        for(int i=0; i<resized.length; i++){
            for(int j=0; j<resized.length; j++){
                resized[i][j] = board[j][i];
            }
        }

        ArrayList<Integer> list = new ArrayList<>();

        for(int i=0; i<moves.length; i++){
            int t = moves[i]-1;
            int tmp = 0;
            while(tmp<resized.length){
                if(resized[t][tmp]!=0){
                    list.add(resized[t][tmp]);
                    resized[t][tmp]=0;
                    break;
                }
                tmp++;
            }
        }

        ArrayList<Integer> tmp = list;
//        for(int i=0; i<tmp.size()-1; i++){
//            if(tmp.get(i) == tmp.get(i+1)){
//                tmp.remove(i);
//                tmp.remove(i);
//                answer+=2;
//                i=0;
//            }
//        }
        
        for(int i=0; i<tmp.size(); i++){
            if(i<tmp.size() -1){
                while(tmp.get(i) == tmp.get(i+1)){
                    tmp.remove(i);
                    tmp.remove(i);
                    answer+=2;
                    if(i!=0)
                        i--;
                    else{
                        i=-1;
                        break;
                    }
                }
            }
        }

        return answer;
    }
}
```
