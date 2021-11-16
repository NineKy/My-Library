# 비밀지도

[https://programmers.co.kr/learn/courses/30/lessons/17681](https://programmers.co.kr/learn/courses/30/lessons/17681)

문제는 위 링크에서 확인

카카오 기출문제이지만 레벨 1이기 때문에 도전해봤고 어렵지 않게 해결!

```java
import java.util.*;
class Solution {
    public String[] solution(int n, int[] arr1, int[] arr2) {
        String[] answer = new String[n];

        for(int i=0; i<n; i++){
            String s1 = check(n, arr1[i]);
//            System.out.println(s1);
            String s2 = check(n, arr2[i]);
//            System.out.println(s2);
            String str = "";
            for(int j=0; j<n; j++){
                if(s1.charAt(j)=='0' && s2.charAt(j)=='0')
                    str+=" ";
                else
                    str+="#";
            }
            answer[i] = str;
        }

        return answer;
    }

    public String check(int size, int num){
        String t = "";
        for(int i=size-1; i>=0; i--){
            int n = (int) Math.pow(2, i);
            if(n <= num){
                num-=n;
                t+="1";
            }else{
                t+="0";
            }
        }
        return t;
    }
}
```
