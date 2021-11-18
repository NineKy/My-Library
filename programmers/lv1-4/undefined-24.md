# 키패드 누르기

[https://programmers.co.kr/learn/courses/30/lessons/67256](https://programmers.co.kr/learn/courses/30/lessons/67256)

레벨 1 카카오 부수기 진핸중이였는데 가장 난관....

2차원 배열처럼 위치를 가정해서 문제 푸는 방식은 생각보다 빠르게 캐치해서 풀고 있었는데

이게 모든 배열위치를 변수화 시켜서 진행하고 있어서 겨우겨우 완성시켜서 테스트케이스를 딱 돌렸더니..? 65점이 나와서 정말 애먹었다... 결국은 다시 짜보자는 생각을 하고 짜고있었는데, 이게 굳이 나눠서 진행할 것도 없이 걍 더해서 비교해도 괜찮다는 생각이 들어서 변수를 최대한 줄이고 만들었는데 성공함..!

```java
import java.util.*;
import java.lang.*;
class Solution {
    public String solution(int[] numbers, String hand) {
        String answer = "";

        int left = 10;
        int right = 12;
        for(int i=0; i<numbers.length; i++){
            if(numbers[i]==1 || numbers[i]==4 || numbers[i]==7){
                answer += "L";
                left = numbers[i];
            }
            else if(numbers[i]==3 || numbers[i]==6 || numbers[i]==9){
                answer += "R";
                right = numbers[i];
            }
            else if(numbers[i]==2 || numbers[i]==5 || numbers[i]==8 || numbers[i]==0){
                if(numbers[i]==0)
                    numbers[i] = 11;
                int ldist = Math.abs(numbers[i]-left)/3 + Math.abs(numbers[i]-left)%3;
                int rdist = Math.abs(numbers[i]-right)/3 + Math.abs(numbers[i]-right)%3;

                if(ldist == rdist){
                    if(hand.equals("right")){
                        answer+="R";
                        right = numbers[i];
                    }else{
                        answer+="L";
                        left = numbers[i];
                    }
                }
                else if(ldist > rdist){
                    answer+="R";
                    right = numbers[i];
                }else{
                    answer+="L";
                    left = numbers[i];
                }
            }
        }

        return answer;
    }

}
```
