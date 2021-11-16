# 콜라츠 추측

[https://programmers.co.kr/learn/courses/30/lessons/12943](https://programmers.co.kr/learn/courses/30/lessons/12943)

레벨 1 문제 부수기 진행중....

이전에 풀다가 못푼거같은데 왜지..?

```java
class Solution {
    public int solution(int num) {
        int answer = 0;
        
        int cnt=0;
        while(num != 1){
            if(num%2==0){
                num/=2;
            }
            else if(num%2==1){
                num = num*3+1;
            }
            cnt++;
            if(cnt==500){
                cnt = -1;
                break;
            }
        }
        answer = cnt;
        
        return answer;
    }
}
```
