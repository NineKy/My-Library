# 숫자 문자열과 영단어

[https://programmers.co.kr/learn/courses/30/lessons/81301](https://programmers.co.kr/learn/courses/30/lessons/81301)

네오와 프로도가 숫자놀이를 하고 있습니다. 네오가 프로도에게 숫자를 건넬 때 일부 자릿수를 영단어로 바꾼 카드를 건네주면 프로도는 원래 숫자를 찾는 게임입니다.다음은 숫자의 일부 자릿수를 영단어로 바꾸는 예시입니다.

* 1478 → "one4seveneight"
* 234567 → "23four5six7"
* 10203 → "1zerotwozero3"

이렇게 숫자의 일부 자릿수가 영단어로 바뀌어졌거나, 혹은 바뀌지 않고 그대로인 문자열 `s`가 매개변수로 주어집니다. `s`가 의미하는 원래 숫자를 return 하도록 solution 함수를 완성해주세요.

참고로 각 숫자에 대응되는 영단어는 다음 표와 같습니다.

대충 이상한 문자열주면 거기서 영어 0부터 9까지를 찾아서 숫자로 변환하고 그 숫자 리턴하는 문제인데,

String 의 replace함수를 사용하면 어렵지 않게 해결할 수 있던 문제

String.replace(바꾸려는 기존의 문자, 바꾸려는 새로운 문자)

```java
import java.util.*;
class Solution {
    public int solution(String s) {
        int answer = 0;
        String[] num = {"0", "1", "2", "3", "4", "5", "6", "7", "8", "9"};
        String[] word = {"zero", "one", "two", "three", "four", "five", "six", "seven", "eight", "nine"};
        for(int i=0; i<num.length; i++){
            s = s.replace(word[i], num[i]);
        }
        return Integer.parseInt(s);
    }
}
```
