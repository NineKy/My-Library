# JadenCase 문자열 만들기



**문제 설명**

JadenCase란 모든 단어의 첫 문자가 대문자이고, 그 외의 알파벳은 소문자인 문자열입니다. 문자열 s가 주어졌을 때, s를 JadenCase로 바꾼 문자열을 리턴하는 함수, solution을 완성해주세요.

**제한 조건**

* s는 길이 1 이상인 문자열입니다.
* s는 알파벳과 공백문자\(" "\)로 이루어져 있습니다.
* 첫 문자가 영문이 아닐때에는 이어지는 영문은 소문자로 씁니다. \( 첫번째 입출력 예 참고 \)

**입출력 예**

| s | return |
| :--- | :--- |
| "3people unFollowed me" | "3people Unfollowed Me" |
| "for the last week" | "For The Last Week" |



```java
import java.util.*;
class Solution {
    public String solution(String s) {
        String answer = "";
        
        //단어별로 자름
        String tmp[] = s.split(" ");

        for(int i=0; i<tmp.length; i++){
            //대문자를 구하기위해 단어를 다시 또 짜름
            String arr[] = tmp[i].split("");

        
            
            arr[0] = arr[0].toUpperCase();
            for(int j=1; j<arr.length; j++){
                arr[j] = arr[j].toLowerCase();
            }
            
            for(int j=0; j<arr.length; j++){
                answer+=arr[j];
            }
            if(i!=tmp.length-1)
                answer+=" ";
        }
        if (" ".equals(s.substring(s.length()-1))) 
            answer += " ";
        
        return answer;
    }
}
```

계속 테스트케이스 8번에서 막혔는데, 질문하기에서보니까 8번은 문자열을 주는데, 맨 마지막에 공백을 줘서 split\(\)함수의 에러를 이용한 테스트였다고 생각한다... 쉽다고 생각했지만 꼼꼼함이 필요한 문제였다...



