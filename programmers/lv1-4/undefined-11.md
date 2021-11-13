# 이상한 문자 만들기

[https://programmers.co.kr/learn/courses/30/lessons/12930](https://programmers.co.kr/learn/courses/30/lessons/12930)

### **문제 설명**

문자열 s는 한 개 이상의 단어로 구성되어 있습니다. 각 단어는 하나 이상의 공백문자로 구분되어 있습니다. 각 단어의 짝수번째 알파벳은 대문자로, 홀수번째 알파벳은 소문자로 바꾼 문자열을 리턴하는 함수, solution을 완성하세요.

### 제한 사항

* 문자열 전체의 짝/홀수 인덱스가 아니라, 단어(공백을 기준)별로 짝/홀수 인덱스를 판단해야합니다.
* 첫 번째 글자는 0번째 인덱스로 보아 짝수번째 알파벳으로 처리해야 합니다.

### 입출력 예

input

```sql
"try hello world"
```

output

```sql
"TrY HeLlO WoRlD"
```

### 입출력 예 설명

"try hello world"는 세 단어 "try", "hello", "world"로 구성되어 있습니다. 각 단어의 짝수번째 문자를 대문자로, 홀수번째 문자를 소문자로 바꾸면 "TrY", "HeLlO", "WoRlD"입니다. 따라서 "TrY HeLlO WoRlD" 를 리턴합니다.

## 풀이

31.3프로만 맞음... → 뭔가 문제길래 안되는걸까 ??????

```java
import java.util.*;
class Solution {
    public String solution(String s) {
        String answer = "";
        s = s.toUpperCase();
        StringTokenizer st = new StringTokenizer(s);
        while(st.hasMoreTokens()){
            String tmp = st.nextToken();
            String res = "";
            for(int j=0; j<tmp.length(); j++){
                if(j%2 == 0){
                    res+=String.valueOf(tmp.charAt(j)).toUpperCase();
                }else{
                    res+=String.valueOf(tmp.charAt(j)).toLowerCase();
                }
            }
            answer+=(res+" ");
        }
        answer = answer.substring(0, answer.length()-1);
        return answer;
    }
}
```

다시 풀어봤는데 흐름 자체는 비슷하게 만들었다.

근데 막혔던 부분은 역시 문자열이 answer의 길이보다 더 긴 공백을 가지고 있는 경우였다..!

이런 경우의 수는 생각도못했다.. 구글링해봐서 찾아낸거지...

```java
import java.util.*;
class Solution {
    public String solution(String s) {
        String answer = "";
        String[] arr = s.split(" ");
        
        for(int i=0; i<arr.length; i++){
            for(int j=0; j<arr[i].length(); j++){
                if(j%2==0)
                    answer+=arr[i].toUpperCase().charAt(j);
                else
                    answer+=arr[i].toLowerCase().charAt(j);
            }
            if(i != arr.length-1)
                answer+=" ";
        }
        if(s.length() > answer.length())
            answer+=s.substring(answer.length());
        return answer;
    }
}
```
