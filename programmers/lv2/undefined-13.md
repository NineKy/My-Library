# 올바른 괄호

[https://programmers.co.kr/learn/courses/30/lessons/12909](https://programmers.co.kr/learn/courses/30/lessons/12909)

음.. 뭐 그렇게 어려운 문제는 아니라고 생각이 듭니다만? 효율성테스트? 요놈에서 막혔네??

```java
class Solution {
    boolean solution(String s) {
        boolean answer = true;

        String[] arr = s.split("");
        int cnt=0;
        for(int i=0; i<arr.length; i++){
            if(arr[i].equals("("))
                cnt++;
            else{
                if(cnt<=0)
                    return false;
                cnt--;
            }
        }

        if(cnt>=1)
            return false;
        
        return answer;
    }
}
```

그러면 맨 처음에 생각이 들었던 스택을 활용해보쟈!

는 스택을 활용해도 계속해서 시간초과가 났다.. 이건 그러면 string.split의 사용때문인건가?

라고 생각해서 character형태의 스택을 활용 → 어느정도는 참고 ㅠ

```java
import java.util.*;
class Solution {
    boolean solution(String s) {
        boolean answer = true;

        // String[] arr = s.split("");
        
        // Stack<String> stack = new Stack<>();
        Stack<Character> stack = new Stack<>();
        for(int i=0; i<s.length(); i++){
            if(s.charAt(i) == '(')
                stack.push(s.charAt(i));
            else{
                if(stack.isEmpty()){
                    return false;
                }else
                    stack.pop();
            }
        }
        
        answer = stack.isEmpty() ? true : false;
        
        return answer;
    }
}
```
