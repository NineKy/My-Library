# 멀쩡한 사각형

[https://programmers.co.kr/learn/courses/30/lessons/62048](https://programmers.co.kr/learn/courses/30/lessons/62048)

문제자체가 엄청 까다롭다는 느낌은 못받아서 풀어봤는데 생각보다 어려웠다...

결국 참고를 해보았고 최소공약수를 사용해서 푸는 방법을 보고 감탄했다!

기존에는 최소공약수를 직접 코드로 짜서 진행했는데 어떤분은

java.math의 라이브러리에 있는 BigInteger타입의 gcd라는 함수를 사용해서 최소공약수를 구하시더라..! 신기했다

```java
import java.util.*;
import java.math.*;
class Solution {
    public long solution(int w, int h) {
        long answer = 1;

        long small, big;
        if(w > h){
            big = Long.valueOf(w);
            small = Long.valueOf(h);
        }else{
            big = Long.valueOf(h);
            small = Long.valueOf(w);
        }

        int num = BigInteger.valueOf(w).gcd(BigInteger.valueOf(h)).intValue();
        

//        long num=0;
//        for(int i=1; i<small; i++){
//            if(small%i==0 && big%i==0){
//                num = i;
//            }
//        }

        answer = (long)w*h-num*(big/num+small/num-1);

        return answer;
    }
}
```
